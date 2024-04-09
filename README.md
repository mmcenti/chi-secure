# Secure

[![Run Tests](https://github.com/gin-contrib/secure/actions/workflows/go.yml/badge.svg?branch=master)](https://github.com/gin-contrib/secure/actions/workflows/go.yml)
[![codecov](https://codecov.io/gh/gin-contrib/secure/branch/master/graph/badge.svg)](https://codecov.io/gh/gin-contrib/secure)
[![Go Report Card](https://goreportcard.com/badge/github.com/gin-contrib/secure)](https://goreportcard.com/report/github.com/gin-contrib/secure)
[![GoDoc](https://godoc.org/github.com/gin-contrib/secure?status.svg)](https://godoc.org/github.com/gin-contrib/secure)

Secure middleware for [Chi](https://github.com/go-chi/chi) framework. This is a fork from the [Gin version](https://github.com/gin-contrib/secure).

## Example

See the [example1](example/code1/example.go), [example2](example/code2/example.go).

DefaultConfig returns a Configuration with strict security settings

[embedmd]:# (secure.go go /func DefaultConfig/ /^}$/)
```go
func DefaultConfig() Config {
	return Config{
		SSLRedirect:           true,
		IsDevelopment:         false,
		STSSeconds:            315360000,
		STSIncludeSubdomains:  true,
		FrameDeny:             true,
		ContentTypeNosniff:    true,
		BrowserXssFilter:      true,
		ContentSecurityPolicy: "default-src 'self'",
		IENoOpen:              true,
		SSLProxyHeaders:       map[string]string{"X-Forwarded-Proto": "https"},
	}
}
```

[embedmd]:# (example/code1/example.go go)
```go
package main

import (
	"log"
	"net/http"

	"github.com/go-chi/chi/v5"
	secure "github.com/mmcenti/chi-secure"
)

func main() {
	router := chi.NewRouter()

	router.Use(secure.New(secure.Config{
		AllowedHosts:          []string{"example.com", "ssl.example.com"},
		SSLRedirect:           true,
		SSLHost:               "ssl.example.com",
		STSSeconds:            315360000,
		STSIncludeSubdomains:  true,
		FrameDeny:             true,
		ContentTypeNosniff:    true,
		BrowserXssFilter:      true,
		ContentSecurityPolicy: "default-src 'self'",
		IENoOpen:              true,
		ReferrerPolicy:        "strict-origin-when-cross-origin",
		SSLProxyHeaders:       map[string]string{"X-Forwarded-Proto": "https"},
	}))

	router.Get("/ping", func(w http.ResponseWriter, r *http.Request) {
		w.WriteHeader(http.StatusOK)
		w.Write([]byte("pong"))
	})

	// Listen and Server in 0.0.0.0:8080
	server := &http.Server{Handler: router}
	if err := server.ListenAndServe(); err != nil {
		log.Fatal(err)
	}
}

```
