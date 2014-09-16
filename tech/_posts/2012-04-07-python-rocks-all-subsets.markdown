---
author: gvivekcbe
comments: true
date: 2012-04-07 22:40:33+00:00
layout: post
slug: python-rocks-all-subsets
title: Python rocks - All subsets
wordpress_id: 190
---

f = lambda x: [[y for j, y in enumerate(set(x)) if (i >> j) & 1] for i in range(2**len(set(x)))]


Just noticed that there is a difference in the meaning of period "." character in regular expression. when it is in the box [] like [\w.]+ it means a literal . and not the regular expression "". whereas anywhere outside of the box, it means the regular expression "." unless it is escaped.

Another thing about "." is it doesnt match newline, and "DOTALL" flag is required for that.


