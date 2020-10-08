## any

TypeScript（型チェッカー）がわからない場合のデフォルトの型。

何でもできるため、意図しない振る舞いをする可能性がある。

そのため最終手段の型であり、できるだけ避けるべき。

### TSCフラグ：noImplicitAny

TypeScriptはanyと推論される値についてはエラーを出さない。

暗黙のanyについてTypeScriptにエラーを吐かせる場合は、<code>tsconfig.json</code>の中で<code>noImplicitAny</code>フラグを有効にする必要がある。

https://typescript-jp.gitbook.io/deep-dive/intro/noimplicitany

## unknown

anyと同様に任意の値を示すが、それが何であるかをチェックし絞り込むまでTypeScriptはunknown型の値の使用を許可しない。

<b>型が事前にわからないような例外的なケースではanyではなく、代わりにunknownを使用すべき。</b>


### unknownの使い方に関する大まかな概念

* a. TypeScriptが何かをunknownと推論することはない − 明示的な型アノテーションが必要
* b. unknown型の値と他の値を比較することは可能
* c. しかし、unknown値が特定の型であることを想定した事柄は行えない
* b. 値が本当にその型であることを示す必要がある

```typescript
let a: unknown = 30          // unknown
let b = a === 123            // boolean
let c = a + 10               // エラー TS2571:オブジェクトの型は'unknown'です。
if (typeof a === 'number') {
  let d = a + 10             // number
}
```
## boolean

boolean型（真偽値型）には、true（真）とfalse（偽）の2つの値がある。

### booleanの使い方に関する大まかな概念

* a,b. 値がbooleanであることをTypeScriptに推論させることが可能
* c. 値が特定のboolean（true or false）であることをTypeScriptに推論させることが可能
* d. 値がbooleanであることをTypeScriptに明示的に伝えるここが可能
* e,f. 値が特定のboolean（true or false）であることをTypeScriptに明示的に伝えることが可能

```typescript
let a = true          // boolean
var b = false         // boolean
const c = true        // true
let d: boolean = true // boolean
let e: true = true    // true
let f: true = false   // エラー TS2322:型'false'を型'true'に割り当てることはできません。
```

### リテラル型（literal type）

<b>リテラル型とはただ1つの値を表し、それ以外の値は受け入れられない型</b>

e、fのケースはboolean trueしか受け入れないためリテラル型である。


## number

numberは以下のすべての数値の集まりである。

* 整数
* 浮動小数点数
* 整数
* 負数
* Infinity（無限大）
* NaN（非数）

etc...

### numberの使い方に関する大まかな概念

* a,b. 値がnumberであることをTypeScriptに推論させることが可能
* c. constを使って、値が特定のnumberであることをTypeScriptに推論させることが可能
* e. 値がnumberであることをTypeScriptに明示的に伝えることが可能
* f,g. 値が特定のnumberであることをTypeScriptに明示的に伝えることが可能

```typescript
let a = 1234            // number 
var b = Infinity * 0.10 // number
const c = 5678          // 5678
let d = a < b           // boolean
let e: number = 100     // number
let f: 26.218 = 26.218  // 26.218
let g: 26.218 = 10      // エラー TS2322:型'10'を型'26.218'に割り当てることはできません。
```


