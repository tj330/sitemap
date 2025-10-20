# sitemap

A small CLI tool that crawls a website (breadth-first) and emits a sitemap XML to stdout.

This repository contains a simple Go program (main.go) that:

- Starts at a given URL
- Traverses links up to a configurable depth (default: 3)
- Restricts crawling to links that share the same base (same host)
- Writes a sitemap in the standard sitemap XML namespace to stdout

The program uses a small HTML link parser (imported as github.com/tj330/link) to extract hrefs from pages.

## Features

- Simple BFS-based crawler
- Same-host filtering
- Outputs standard sitemap XML (`http://www.sitemap.org/schemas/sitemap/0.9`)
- Minimal dependencies (Go standard library + link parser)

## Requirements

- Go 1.18+ (or a recent Go toolchain)
- Network access to the target site

## Installation

Clone the repository and build with the Go toolchain:

```bash
git clone https://github.com/tj330/sitemap.git
cd sitemap
go mod tidy
go build -o sitemap ./...
```

Or run directly with `go run`:

```bash
go run main.go
```

If the link parser is not present in your module cache, `go mod tidy` or `go build` will fetch `github.com/tj330/link`.

## Usage

The binary prints sitemap XML to stdout.

Flags:

- `-url` — the start URL to crawl (default: `https://tj330.com`)
- `-depth` — the maximum number of link-depths to traverse (default: `3`)

Examples:

Crawl the default site with default depth:

```bash
./sitemap
```

Crawl a different site with depth 2:

```bash
./sitemap -url https://example.com -depth 2
```

You can redirect the output to a file:

```bash
./sitemap -url https://example.com -depth 2 > sitemap.xml
```

## How it works (brief)

- bfs(url, maxDepth) performs a breadth-first traversal starting from the provided URL.
- get(url) fetches the page, resolves the page base (scheme + host), extracts hrefs, and filters them to only include links that start with that base.
- hrefs(...) expands relative links that start with `/` by prefixing them with the base (scheme + host), and keeps absolute `http...` links as-is.
- The collected URLs are then encoded into a simple sitemap XML document with the proper `xmlns`.
