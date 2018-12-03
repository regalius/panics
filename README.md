# Panics [![GoDoc](http://img.shields.io/badge/go-documentation-blue.svg?style=flat-square)](http://godoc.org/github.com/alileza/panics) [![CircleCI](https://circleci.com/gh/alileza/panics/tree/master.png?style=shield)](https://circleci.com/gh/alileza/panics/tree/master) [![GoReportCard](https://goreportcard.com/badge/github.com/alileza/panics)](https://goreportcard.com/report/github.com/alileza/panics)
Simple package to catch & notify your panic or exceptions via slack or save into files.

```go
import "github.com/tokopedia/panics"
```

## Configuration
```go
panics.SetOptions(&panics.Options{
	Env:             "TEST",
	SlackWebhookURL: "https://hooks.slack.com/services/blablabla/blablabla/blabla",
	Filepath:        "/var/log/myapplication", // it'll generate panics.log
	Channel:         "slackchannel",

	Tags: panics.Tags{"host": "127.0.0.1", "datacenter":"aws"},
})
```

## Capture Custom Error
```go
panics.Capture(
    "Deposit Anomaly",
    `{"user_id":123, "deposit_amount" : -100000000}`,
)
```

## Capture Panic on HTTP Handler
```go
http.HandleFunc("/", panics.CaptureHandler(func(w http.ResponseWriter, r *http.Request) {
	panic("Duh aku panik nih guys")
}))
```

## Capture Panic on httprouter handler
```go
router := httprouter.New()
router.POST("/", panics.CaptureHTTPRouterHandler(func(w http.ResponseWriter, r *http.Request, _ httprouter.Params) {
    panic("Duh httprouter aku panik nih guys")
}))
```

## Capture Panic on negroni custom middleware
```go
http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
	panic("Duh aku panik nih guys")
})
negro := negroni.New()
negro.Use(negroni.HandlerFunc(CaptureNegroniHandler))
```

# Capture panic on nsq consumer
```go
q, _ := nsq.NewConsumer("topic", "channel", nsq.NewConfig())

q.AddHandler(panics.CaptureNSQConsumer(func(message *nsq.Message) error {
	var x *int
	fmt.Println(*x)
	message.Finish()
	return nil
}))
```

# Use panic as middleware on standard http.Handler
```go
//for example we use chi router
r := chi.NewRouter()
r.Use(panics.HTTPRecoveryMiddleware)
r.Get("/", func(w http.ResponseWriter, r *http.Request) {
	panic("panic from affiliate server")
})
```

# Use panic as server interceptor on GRPC Server
```go
server := grpc.NewServer(
	grpc.UnaryInterceptor(grpc_middleware.ChainUnaryServer(
		// use another interceptor
		grpc_validator.UnaryServerInterceptor(),
		// use panic's unary interceptor
		panics.UnaryServerInterceptor,
	)),
)
```

## Example
### Slack Notification
![Notification Example](https://monosnap.com/file/Pjkw1uxjV8p0GnjevDwhHesUnTC2Ru.png)

# Authors

* [Ali Reza](mailto:https://github.com/alileza)
* [Afid Eri](mailto:afid.eri@gmail.com)
* [Albert Widiatmoko](https://github.com/albert-widi)
