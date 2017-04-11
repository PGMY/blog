---
layout: post
title: "Swift3 処理時間の計測"
date: 2016-11-10 11:11:07 +0900
comments: true
category: Swift3
tags: [snippets]
keywords:
published: true
---

```
let start = Date()
// 処理
let elapsed = Date().timeIntervalSince(start)
print(elapsed)
```

```
class Benchmark {

    // 開始時刻を保存する変数
    var startTime: Date
    var key: String

    // 処理開始
    init(_ key: String) {
        self.startTime = Date()
        self.key = key
    }

    // 処理終了
    func finish() {
        let elapsed = Date().timeIntervalSince(self.startTime) as Double
        let formatedElapsed = String(format: "%.3f", elapsed)
        print("Benchmark: \(key), Elasped time: \(formatedElapsed)(s)")
    }

    // 処理をブロックで受け取る
    class func measure(key: String, block: () -> ()) {
        let benchmark = Benchmark(key)
        block()
        benchmark.finish()
    }
}
```

[Swiftで重い処理の処理時間を測定する](http://qiita.com/tady/items/40d7c4feecda337cf271)
