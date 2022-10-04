# First Experience with Crystal: Using OpenAPI Generator built API client SDKs 

Last weekend I dived into Crystal for the first time, despite knowing about the language and being interested in it for a long time. In a weekend, I was successful using Crystal for the first time, [building my first shard](https://github.com/grokify/crystal-pingpong), [building an API client SDK](https://github.com/grokify/crystal-financialmodelingprep) [using OpenAPI Generator](https://github.com/OpenAPITools/openapi-generator), and [calling the API client SDK](https://github.com/grokify/crystal-financialmodelingprep-example/blob/main/main.cr). Here's my a quick overview of my experience which was quite good overall. I wanted to write down my first impressions before they drift away.

First off, I have a Ruby background from a while back and had published a few Gems, but had recently moved to Go for its static typing and ended up enjoying many other features.

My goal was to create and use an API client SDK using OpenAPI Generator Project. I wanted to build a representative SDK to get the feel for it. I have a number of commits to OpenAPI Generator for Go, so I'm familiar with the project.

## Installation

I use a MacBook and Crystal was okay to install but relative to Go required more steps, reminding me of installing other languages in the past. While I could just download and install Go, for Crystal, I needed to use Homebrew, upgrade PCRE, and ran into a LLVM issue. It didn't take long to do, but did require thinking and searching for more items.

## Creating my first shard

Before using an auto-generated shard, I wanted to create a small shard just to understand the process. I used the following resources which got me up and running.

1. Docs: https://crystal-lang.org/reference/1.5/guides/writing_shards.html - good baseline info
2. Tutorial https://veelenga.github.io/make-your-own-shard-in-crystal-language/ - appreciated testing via `crystal `spec`
1. Example Shard: https://github.com/mamantoha/twitter-crystal
4. Example Shard: https://github.com/comandeo/crystal-memcached

From the above, I created the simplest shard possible and called it from a Crystal program.

* Simple shard: https://github.com/grokify/crystal-pingpong

A consideration with this is that I created it with the shard name `pingpong` via `% crystal init lib pingpong` but pushed it to `github.com/grokify/crystal-pingpong`.

Eventually, I found the following, but only after finishign the project and writing this article:

1. Crystal Shards: https://github.com/crystal-lang/shards
2. Crystal man page: https://man.archlinux.org/man/shard.yml.5.en

## Creating the API client using OpenAPI Generator

I wanted to create a very small client that I could execute, so I created an OpenAPI spec for an API I've been using recently, Financial Modeling Prep, to get financial statement info.

The docs for OpenAPI Generator for Crystal are here:

* OpenAPI Generator Crystal Docs: https://openapi-generator.tech/docs/generators/crystal/

The SDK is on GitHub with a number of issues I ran into documented on GitHub:

* API Client: https://github.com/grokify/crystal-financialmodelingprep
* Issues: https://github.com/grokify/crystal-financialmodelingprep/issues?q=is%3Aissue+is%3Aclosed
* Demo App: https://github.com/grokify/crystal-financialmodelingprep-example

The first issue was the lack of a module name, which doesn't appear to be listed in the official docs, but I found via what looks to be a pre-cursor to the official OAG product support identifying the `moduleName` additional property.

https://github.com/cyangle/crystal_client_generator

I found this looking for a Google client here:

https://github.com/cyangle/google_drive

There are a few other issues listed in the GitHub issues, but they were straight-forward to diagnose and overcome. Some issues were in the spec I created.

The working demo code calling the SDK is as follows:

```crystal
require "financialmodelingprep"

api = Financialmodelingprep::FinancialStatementsApi.new

infos, status, headers = api.get_income_statements_with_http_info(
  symbol: "AAPL",
  apikey: ENV["FMP_API_KEY"] || "",
  datatype: "json",
  limit: 1,
  period: "annual")

puts status

infos.each { |info|
  puts info.to_json
}
```

## Observations

Here are some final observations. My overall experience is that building the SDK using a combination of docs, tutorials, example shards found via Google, and error messages got me through successfully.

1. Pre-requisites: Crystal is easy, but it would be nice to not worry about Homebrew, PCRE and LLVM.
2. Shard Name vs. Module Name: it would be nice if the `shard.yml` dependences section only needed the git path, instead of the shard name, similar to Go's `go.mod` file. In my case, my GitHub project is `github.com/grokify/crystal-financialmodelingprep` but my shard name is `financialmodelingprep`.
3. Dependencies: I'm a big fan of using `git` and `github` directly so I liked having this info directly in `shard.yml`, without an intermediate packaging systems as it makes publishing and updating shares much easier. `github` may be a nice shorthand, but consistency is nice.
3. lib folder: this reminds of me ofthe `node_modules` folder. I liked having this as it allowed me to debug/enhance dependencies quickly.
4. Naming magic: I don't fully recall all the naming conventiosn of convering hypens or underscores to camelCase from Ruby yet yet, so to get around this, I switched to Go's convention of not haivng hypens/underscores in folder and package names.

My closing thoughts are that it's easy enough to use and I really enjoyed using Ruby syntax again. I look forward to using more Crystal in the future.