---
published: true
title: Constraint Priority 1000 cannot be modified.
layout: post
---
* Let's get started....for programming blog.

* Auto layouts 에서 Constraint 는 1000으로 설정된 상태에서 이를 code 에서 250이나 750등 으로 변경하면 Crash 발생함

* Suggestion: 750 또는 250으로 해 줄것.

* 아래처럼 하면  UI 를 실시간(?)으로 변경 가능함.

```objc

_constraintTodayViewToSubtitle.priority = 750;
_constraintTodayViewToSuperview.priority = 250;
[self setNeedsUpdateConstraints];

```