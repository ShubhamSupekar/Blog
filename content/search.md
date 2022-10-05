---
title: "Search" # in any language you want
layout: "search" # is necessary
#url: "/archives/"
#description: "Description for Search"
summary: "search"
placeholder: "search"
searchHidden: true
params:
    fuseOpts:
        isCaseSensitive: false
        includeMatches: true
        shouldSort: true
        location: 0
        distance: 1000
        threshold: 0.4
        minMatchCharLength: 0
        keys: ["title", "permalink", "summary", "content"]
---

