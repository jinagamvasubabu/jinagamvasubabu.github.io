---
layout: post
comments: true
title: Upgrading Go Version to 1.13 is not a cakewalk!!!!
categories: [graph, data science]
thumbnail: /images/zoommiddleeast.png
---

## Why it's not a `cakewalk`?

Be aware that upgrading the version of Go from older versions to 1.13 is not so easy, it comes with new difficulties due to these recent changes:
* Version validation:
   * In 1.13 Go performs additional validation on the requested version string. The `+incompatible` version annotation bypasses the requirement of semantic import versioning for repositories that predate the introduction of modules. The go command now verifies that such a version does not include an explicit go.mod file.
  Refer for more info `https://golang.org/doc/go1.13#/version-validation` 
* Changes to the Environment Variable `https://golang.org/doc/go1.13#/proxy-vars`
* Proxy Servers:
   * If your company having private repositories then you need to set these below environment variables
       * go env -w GONOSUMDB="****" # use your github or bitbucket repo base url
       * go env -w GONOPROXY="****" # use your github or bitbucket repo base url
       * go env -w GOPROXY="https://proxy.golang.org,direct" #this should already be the default
       * go env -w GOPRIVATE="****" # use your github or bitbucket repo base url

Before start upgrading first clean your mod cache:
`GO111MODULE=on go clean -modcache`

I will be listing out the possible errors and it's respective solutions as well. 

#### #1: If you are using go-micro framework
```
go: bitbucket.org/****/**** requires
    github.com/micro/go-plugins@v0.25.0 requires
    gocloud.dev@v0.10.0 requires
    contrib.go.opencensus.io/exporter/ocagent@v0.4.2 requires
    github.com/census-instrumentation/opencensus-proto@v0.1.0-0.20181214143942-ba49f56771b8: invalid pseudo-version: version before v0.1.0 would have   negative patch number
``` 

#### fix:
```
Add this at last in your `go.mod` file
replace github.com/census-instrumentation/opencensus-proto v0.1.0-0.20181214143942-ba49f56771b8 => github.com/census-instrumentation/opencensus-proto v0.0.3-0.20181214143942-ba49f56771b8
```


#### #2: `Protobuf` related problems
```
go: k8s.io/api@v0.0.0-20190726022912-69e1bce1dad5 requires
    github.com/gogo/protobuf@v0.0.0-20190410021324-65acae22fc9: invalid pseudo-version: revision is shorter than canonical (65acae22fc9d)
S
```

#### fix:
```
Add this at last in your `go.mod` file
replace github.com/gogo/protobuf v0.0.0-20190410021324-65acae22fc9 => github.com/gogo/protobuf v0.0.0-20190723190241-65acae22fc9d
```


#### #3: `github.com/instana/go-sensor` related


#### fix:
```
Add this at last in your `go.mod` file
replace github.com/instana/go-sensor => github.com/instana/golang-sensor v1.5.0
```


#### #4: Issue with `mailgun-go`

#### fix:
```
Add this at last in your `go.mod` file
replace github.com/mailgun/mailgun-go => github.com/mailgun/mailgun-go v0.0.0-20180718145431-bbe57f98d550
```

#### #5: Issue with `hashicorp` ambiguous import issue
```
cannot load github.com/hashicorp/consul/api: ambiguous import: found github.com/hashicorp/consul/api in multiple modules:
        github.com/hashicorp/consul v1.4.4 (/Users/vasubabujinagam/go/pkg/mod/github.com/hashicorp/consul@v1.4.4/api)
        github.com/hashicorp/consul/api v1.1.0 (/Users/vasubabujinagam/go/pkg/mod/github.com/hashicorp/consul/api@v1.1.0)
S
```

#### fix:
```
Add this at last in your `go.mod` file
replace github.com/hashicorp/consul => github.com/hashicorp/consul v1.5.1
```


if you are facing anything other than this, please post in the comment section.
