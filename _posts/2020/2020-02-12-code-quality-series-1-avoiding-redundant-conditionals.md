---
title: 'Code Quality Series #1 — Avoiding Redundant Conditionals'
description: How to simplify Java conditionals by removing redundancy, improving readability, and making your code more maintainable with industry best practices.
date: 2020-02-12 18:12:12 +0500
last_modified_at: 2025-09-25 14:14:23 +0500
published: true
categories: [Software Engineering, Code Quality]
tags: [java, clean-code, best-practices, static-analysis]
mermaid: false
media_subpath: /assets/media/2020/code-quality-series-1-avoiding-redundant-conditionals/
image: code-quality-1.webp
---

## Introduction

Writing clean and maintainable code is not just about making
it work, it’s about making it understandable,
scalable, and error-resistant. One common issue that developers often overlook is the presence of
redundant conditionals. These add unnecessary complexity, make code harder to read, and sometimes even mask bugs.

In this post, we’ll explore through an example and show how simplifying conditionals leads to clearer
and more maintainable code.

> 💡 Available since Java 16 and widely adopted in production codebases using Java 17+ onwards.
It’s clean and eliminates redundant casting (`(String) object`).  
> 💡 In production, CI/CD pipelines often integrate static analysis tools (SonarQube, Checkstyle, PMD),
which can automatically catch redundant conditionals. However, understanding and handling them beforehand is always better.
{: .gh-alert.note }

## What are redundant conditionals & How can we spot them?

Consider the following method:

```java
public static boolean isNullable(Object object) {
    if (object != null) {
        if (object instanceof String) {
            String strVal = (String) object;
            if (strVal.trim().equals("") ||
                strVal.trim().equals("null")) {
                return true;
            } else {
                return false;
            }
        }
    }
    return true;
}
```

### What’s wrong here?

At first glance, this may look like a normal method, but it carries hidden costs. Do you know why?

- Nested conditionals – too many `if` blocks make the logic hard to follow.
- Redundancy – returning true or false inside an `if/else` is unnecessary when the condition itself already evaluates
  to a boolean.
- Readability – the method intent `(check if string is null-like)` is buried under boilerplate.

## How to avoid them ?

Depending on the Java version your codebase is based on, refactoring may differ.

### ✅ on Java (17+ with Pattern Matching)

```java
public static boolean isNullable(Object object) {
    if (object instanceof String strVal) {
        return strVal.trim().isEmpty() || strVal.trim().equals("null");
    }
    return object == null;
}
```

As you may see it is cleaner, uses pattern matching for `instanceof` (Java 16+), and directly returns boolean expressions.

### ✅ More Concise with Objects + Pattern Matching

If you aim for maximum readability, especially in modern codebases that rely on streams and functional style
we can further refactor above

```java
public static boolean isNullable(Object object) {
  return Objects.isNull(object) || 
       (object instanceof String str && (str.isBlank() || "null".equals(str)));
}
```

### ✅ For Legacy-Friendly (Java 8/11 Compatible)

```java
public static boolean isNullable(Object object) {
    if (object instanceof String) {
        String strVal = (String) object;
        return strVal.trim().isEmpty() || strVal.trim().equals("null");
    }
    return object == null;
}
```

Although it is slightly verbose still common in enterprise projects.

## What are the benifits?

- Improves readability by removing unnecessary nesting.
- Avoids redundancy in returning booleans.
- Complies with static analysis tools (SonarQube, Checkstyle, PMD).
- Supports maintainability by making intent clear.

> ✍️ This updated post is part of my "Code Quality" series, where I share practical examples
and industry best practices from my experience over the years, and the original base article can also be found
on [Medium](https://medium.com/@NiranjanLiyanage/code-quality-security-static-code-analysis-894e270e2f38).
