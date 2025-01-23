---
weight: 4
---

# Response Auto Parse

Out of the box, Resty does response automatic unmarshaling for JSON and XML based on the response header `Content-Type` with methods [Request.SetResult]({{% godoc v3 %}}Request.SetResult) or [Request.SetError]({{% godoc v3 %}}Request.SetError) are used.

For handling custom content-type or customized parsing, see [Content-Type {Encoder, Decoder}]({{% relref "content-type-encoder-and-decoder" %}}).

## Examples

```go
res, err := client.R().
    SetBody(User{
        Username: "testuser",
        Password: "testpass",
    }). // default request content-type is JSON
    SetResult(&LoginResponse{}).
    SetError(&LoginError{}).
    Post("https://myapp.com/login")

fmt.Println(err)
fmt.Println(res.Result().(*LoginResponse))
fmt.Println(res.Error().(*LoginError))
```

### SetResult and SetError Usage

{{% hint info %}}
Examples describe the method `SetResult`, which applies to `SetError`.
{{% /hint %}}

#### Usage 1 - Inline Pointer

```go
// set
client.R().SetResult(&LoginResponse{})

// access
fmt.Println(res.Result().(*LoginResponse))
```

#### Usage 2 - Non-Pointer

```go
// set
client.R().SetResult(LoginResponse{})

// access
fmt.Println(res.Result().(*LoginResponse))
```

#### Usage 3 - Pointer

```go
loginResponse := &LoginResponse{}
// set
client.R().SetResult(loginResponse)

// access
fmt.Println(loginResponse)
```

## Expect Content-Type

It provides a fallback Content-Type for automatic unmarshalling when the response header Content-Type is unavailable.

```go
client.R().SetExpectResponseContentType("application/json")
```

## Force Content-Type

It forces the Content-Type for automatic unmarshalling to ignore the response header Content-Type value.

```go
client.R().SetForceResponseContentType("application/json")
```

## Do Not Parse

To prevent automatic response parsing for the particular use case, use this setting.

{{% hint warning %}}
Using the do not parse option means:
* You have taken over the control of response body parsing from Resty.
* Do not forget to close the response body. Otherwise, you might get into connection leaks, and connection reuse may not happen.
{{% /hint %}}

{{% hintreqoverride %}}

```go
res, err := client.R().
    SetDoNotParseResponse(true).
    Get("https://httpbin.org/json")
if err != nil {
    fmt.Println(err)
    return
}

defer res.Body.Close() // ensure to close response body

resBytes, err := io.ReadAll(res.Body)
if err != nil {
    fmt.Println(err)
    return
}

fmt.Println("Response:", string(resBytes))
```

## Methods

### Client

* [Client.SetError]({{% godoc v3 %}}Client.SetError)
* [Client.SetDoNotParseResponse]({{% godoc v3 %}}Client.SetDoNotParseResponse)

### Request

* [Request.SetResult]({{% godoc v3 %}}Request.SetResult)
* [Request.SetError]({{% godoc v3 %}}Request.SetError)
* [Request.SetExpectResponseContentType]({{% godoc v3 %}}Request.SetExpectResponseContentType)
* [Request.SetForceResponseContentType]({{% godoc v3 %}}Request.SetForceResponseContentType)
* [Request.SetDoNotParseResponse]({{% godoc v3 %}}Request.SetDoNotParseResponse)

### Response

* [Response.IsSuccess]({{% godoc v3 %}}Response.IsSuccess)
* [Response.IsError]({{% godoc v3 %}}Response.IsError)
* [Response.Result]({{% godoc v3 %}}Response.Result)
* [Response.Error]({{% godoc v3 %}}Response.Error)
* [Response.Body]({{% godoc v3 %}}Response)
* [Response.String]({{% godoc v3 %}}Response.String)
* [Response.Bytes]({{% godoc v3 %}}Response.Bytes)
* [Response.Status]({{% godoc v3 %}}Response.Status)
* [Response.StatusCode]({{% godoc v3 %}}Response.StatusCode)
* [Response.Proto]({{% godoc v3 %}}Response.Proto)
* [Response.Header]({{% godoc v3 %}}Response.Header)
* [Response.Cookies]({{% godoc v3 %}}Response.Cookies)
* [Response.Time]({{% godoc v3 %}}Response.Time)
* [Response.ReceivedAt]({{% godoc v3 %}}Response.ReceivedAt)
* [Response.Size]({{% godoc v3 %}}Response.Size)
* [Response.RedirectHistory]({{% godoc v3 %}}Response.RedirectHistory)
