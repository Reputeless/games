# ゲーム典型 (C++ & Siv3D)

様々なゲームのプログラムを C++ & Siv3D で書いていくプロジェクトです。ほとんどの行にコメントが付いていて、学習の参考にしやすいコードになっています。

- コードのライセンスはパブリックドメインで、自由に再利用できます
- 派生 / 改造プログラムを作ったら、ぜひ Zenn や Qiita に投稿してみてください
  - その際に、このリポジトリにリンクしてもらえるとありがたいです
- ゲームの番号 (001A など) は一意 & 将来にわたって固定です
- バグや改善案の提案、リクエストなどはこのリポジトリの Issue をご利用ください

### ゲーム一覧

| 番号 | ゲーム | 難易度 | キーワード |
|:---:|:---|:---:|:---|
| 001A | [ブロックくずし](games/001/A.md) | ★2 | 図形クラスを活用しよう |
| **001B** | [再挑戦可能なブロックくずし](games/001/B.md) | ★2 | 初期状態を作る関数を再利用しよう |
| **002A** | [タイピングゲーム](games/002/A.md) | ★1 | `TextInput::Update()` を活用しよう |
| **003A** | [神経衰弱](games/003/A.md) | ★3 | トランプの描画は `PlayingCard` |
| **004A** | [ハノイの塔](games/004/A.md) | ★2 | 配列を使ってコードを短くしよう |
| 005A | [2D 物理演算による破壊ゲーム](games/005/A.md) (Angry Birds など) | ★3 | `P2Body::setVelocity()` で発射速度を設定 |
| **005B** | [2D 物理演算による破壊ゲーム - 予測軌道を表示](games/005/B.md) | ★3 | 物理演算機能に頼らない方法もある |
| **006A** | [Wheel of Fortune (ルーレット)](games/006/A.md) | ★3 | 結果につじつまを合わせよう |
| **007A** | [2D RPG のマップと移動の基本](games/007/A.md) | ★3 | マップの情報は外部ファイルに用意しよう |
| 008A | [マインスイーパーの盤面](games/008/A.md) | ★2 |  |
| 008B | [マインスイーパーの島構造の構築](games/008/B.md) | ★4 |  |
| 008C | [マインスイーパーのマスを開く](games/008/C.md) | ★5 |  |
| **008D** | [マインスイーパー](games/008/D.md) | ★6 |  |


### サムネイル画像から探す
|  |  |  |  |
|:---:|:---:|:---:|:---:|
|<a href="games/001/B.md"><img src="https://raw.githubusercontent.com/Reputeless/games/main/games/001/B.png" width="144px"></a><br>001B|<a href="games/002/A.md"><img src="https://raw.githubusercontent.com/Reputeless/games/main/games/002/A.png" width="144px"></a><br>002A|<a href="games/003/A.md"><img src="https://raw.githubusercontent.com/Reputeless/games/main/games/003/A.png" width="144px"></a><br>003A|<a href="games/004/B.md"><img src="https://raw.githubusercontent.com/Reputeless/games/main/games/004/A.png" width="144px"></a><br>004A|
|<a href="games/005/B.md"><img src="https://raw.githubusercontent.com/Reputeless/games/main/games/005/B.png" width="144px"></a><br>005B|<a href="games/006/A.md"><img src="https://raw.githubusercontent.com/Reputeless/games/main/games/006/A.png" width="144px"></a><br>006A|<a href="games/007/A.md"><img src="https://raw.githubusercontent.com/Reputeless/games/main/games/007/A.png" width="144px"></a><br>007A|<a href="games/008/D.md"><img src="https://raw.githubusercontent.com/Reputeless/games/main/games/008/D.png" width="144px"></a><br>008D|


### 参考リンク
- [Siv3D 公式リポジトリ](https://github.com/Siv3D/OpenSiv3D)
- [Siv3D 公式サイト](https://siv3d.github.io/ja-jp/)
- [Siv3D 公式リファレンス](https://zenn.dev/reputeless/books/siv3d-documentation)


### 姉妹プロジェクト
- [競プロ典型 90 問 練習 (C++17)](https://github.com/Reputeless/tenkei_90)
