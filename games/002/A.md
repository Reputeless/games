# タイピングゲーム

![](A.png)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.3

void Main()
{
	// 背景色を設定する
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// 出題テキストのリスト
	const Array<String> texts =
	{
		U"Practice makes perfect.",
		U"Don't cry over spilt milk.",
		U"Faith will move mountains.",
		U"Nothing ventured, nothing gained.",
		U"Bad news travels fast.",
	};

	// 出題されているテキスト
	// texts の中からランダムに 1 つ選ぶ
	String target = texts.choice();

	// プレイヤーが入力したテキスト
	String input;

	// テキスト表示用のフォント
	const Font font{ 40, Typeface::Bold };

	while (System::Update())
	{
		////////////////////////////////
		//
		//	状態更新
		//
		////////////////////////////////

		// テキスト入力（TextInputMode::DenyControl: エンターやタブ、バックスペースは受け付けない）
		TextInput::UpdateText(input, TextInputMode::DenyControl);

		// 誤った入力が含まれていたら削除
		while (not target.starts_with(input))
		{
			input.pop_back();
		}

		// 出題されているテキストとプレイヤーが入力したテキストが一致したら
		if (input == target)
		{
			// texts の中からランダムに 1 つ選ぶ
			target = texts.choice();

			// プレイヤーが入力したテキストを消去する
			input.clear();
		}

		////////////////////////////////
		//
		//	描画
		//
		////////////////////////////////

		// 出題テキストを表示
		font(target).draw(40, 270, ColorF{ 0.75 });

		// プレイヤーの入力テキストをその上に表示
		font(input).draw(40, 270, ColorF{ 0.1 });
	}
}
```
