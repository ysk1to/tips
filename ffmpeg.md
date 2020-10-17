## movファイルをgifファイルに変換

* 3倍速
* 690px

```
$ ffmpeg -i hoge.mov -r 24 -filter_complex "setpts=PTS/3.0,scale=690:-1,split [a][b];[a] palettegen [p];[b][p] paletteuse" hoge.gif
```


## 確認

```
$ open -a /Applications/Google\ Chrome.app example.gif
```

