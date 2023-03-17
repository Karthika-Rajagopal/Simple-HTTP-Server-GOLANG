# Simple-HTTP-Server-GOLANG


In Go, the Handle and HandleFunc functions from the http package are used to register handlers for requests in http servers. If it’s not clear when to use one or another, here is the difference. In short, Handle expects a Handler type, and HandleFunc expects a func(ResponseWriter, *Request) function. Here’s an example.

package main

import (
	"fmt"
	"net/http"
)

func main() {

	// http.Handle expects a Handler
	var helloRedirect http.Handler = http.RedirectHandler("/hello", 301)
	http.Handle("/hey", helloRedirect)

	// http.HandleFunc expects a function
	http.HandleFunc("/hello", func(w http.ResponseWriter, r *http.Request) {
		fmt.Fprintf(w, "hello")
	})

	http.ListenAndServe(":8080", nil)
}
http.Handle
The Handle function registers objects implementing the interface Handler that should be able to serve HTTP requests for a given path pattern.

func Handle(pattern string, handler Handler) { ... }
The Handler interface especifies a single method that may be called by the server multiplexer (ServerMux) when dispatching incoming HTTP requests to the appropriate handler objects.

type Handler interface {
  ServeHTTP(ResponseWriter, *Request)
}
There are other types implementing this interface, like the ones created by the RedirectHandler(...) or TimeoutHandler(...) that could be used. Also, you can create your own type implementing ServeHTTP(ResponseWriter, *Request) and use it as a custom handler.

http.HandleFunc
The HandleFunc function expects a function with signature as func(ResponseWriter, *Request)) instead of a direct Handler implementation.

func HandleFunc(pattern string, handler func(ResponseWriter, *Request)) { ... }
This signatures respects the function type HandlerFunc, which also satisfy the Handler interface by providing an implementation to ServeHTTP.

type HandlerFunc func(ResponseWriter, *Request)

// ServeHTTP calls f(w, r).
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {
  f(w, r)
}
That means HandlerFunc implements Handler and can be used seamlessly by the server mux to handle HTTP requests. In other words, you can use a function with that same signature as your custom handler instead of creating a type implementing the ServeHTTP method.
