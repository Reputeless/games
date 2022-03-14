# 再プレイ可能なブロックくずし

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.3

////////////////////////////////
//
//	定数
//
////////////////////////////////

// 画面のサイズ (800x600)
constexpr Size SceneSize = Scene::DefaultSceneSize;

// 1 つのブロックのサイズ
constexpr Size BrickSize{ 40, 20 };

// 横に何個のブロックが並ぶか
constexpr int32 BrickCountX = (SceneSize.x / BrickSize.x);

// 縦に何個のブロックが並ぶか
constexpr int32 BrickCountY = 5;

// ブロックを並べ始める座標
constexpr Point BrickStartPosition{ 0, 60 };

// ボールの初期位置（ピクセル）
constexpr Vec2 BallInitialPos{ (SceneSize.x * 0.5), (SceneSize.y * 0.75) };

// ボールの速さ（ピクセル / 秒）
constexpr double BallSpeedPerSec = 480.0;

// パドルの Y 座標
constexpr int32 PaddleY = 500;

// パドルのサイズ
constexpr Size PaddleSize{ 60, 10 };

////////////////////////////////
//
//	初期状態を作る関数
//
////////////////////////////////

// ブロック（brick: レンガ）の初期配列を作る関数
Array<Rect> MakeBricks()
{
	// ブロックの配列（1 つのブロックを Rect で表現）
	Array<Rect> bricks;

	for (int32 y = 0; y < BrickCountY; ++y)
	{
		for (int32 x = 0; x < BrickCountX; ++x)
		{
			// ブロックの左上の X 座標
			const int32 posX = (x * BrickSize.x + BrickStartPosition.x);

			// ブロックの左上の Y 座標
			const int32 posY = (y * BrickSize.y + BrickStartPosition.y);

			// Rect を追加
			bricks << Rect{ posX, posY, BrickSize };
		}
	}

	return bricks;
}

// 初期のボールを作成する関数
Circle MakeBall()
{
	return{ BallInitialPos, 8 };
}

// 初期のボールの速度を作成する関数
Vec2 MakeBallVelocity()
{
	return{ 0, -BallSpeedPerSec };
}

////////////////////////////////
//
//	Main
//
////////////////////////////////

void Main()
{
	// ブロックの配列
	Array<Rect> bricks = MakeBricks();

	// ボール（中心座標と半径）
	Circle ball = MakeBall();

	// ボールの速度
	Vec2 ballVelocity = MakeBallVelocity();

	while (System::Update())
	{
		////////////////////////////////
		//
		//	状態の更新
		//
		////////////////////////////////

		// パドル
		const Rect paddle{ Arg::center(Cursor::Pos().x, PaddleY), PaddleSize };

		// ボールを移動
		ball.moveBy(ballVelocity * Scene::DeltaTime());

		// ブロックを順にチェック
		for (auto it = bricks.begin(); it != bricks.end(); ++it)
		{
			// ブロックとボールが交差していたら
			if (it->intersects(ball))
			{
				// ブロックの上辺、または底辺と交差していたら
				if (it->bottom().intersects(ball)
					|| it->top().intersects(ball))
				{
					// ボールの速度の Y 成分を反転
					ballVelocity.y *= -1;
				}
				else
				{
					// ボールの速度の X 成分を反転
					ballVelocity.x *= -1;
				}

				// ブロックを配列から削除（イテレータは無効になるので注意）
				bricks.erase(it);

				// これ以上チェックしない
				break;
			}
		}

		// 天井にぶつかったら
		if ((ball.y < 0) && (ballVelocity.y < 0))
		{
			// ボールの速度の Y 成分を反転
			ballVelocity.y *= -1;
		}

		// 左右の壁にぶつかったら
		if (((ball.x < 0) && (ballVelocity.x < 0))
			|| ((SceneSize.x < ball.x) && (0 < ballVelocity.x)))
		{
			// ボールの速度の X 成分を反転
			ballVelocity.x *= -1;
		}

		// パドルにあたったら
		if ((0 < ballVelocity.y) && paddle.intersects(ball))
		{
			// パドルの中心からの距離に応じてはね返る方向（速度ベクトル）を変える
			ballVelocity = Vec2{ (ball.x - paddle.center().x) * 10, -ballVelocity.y }
				.setLength(BallSpeedPerSec); // ボールの速さが BallSpeedPerSec になるよう、ベクトルの長さを調整
		}

		// 画面の底を越えたら
		if (SceneSize.y <= ball.y)
		{
			// ブロックの配列をリセット
			bricks = MakeBricks();

			// ボールをリセット
			ball = MakeBall();

			// ボールの速度をリセット
			ballVelocity = MakeBallVelocity();
		}

		////////////////////////////////
		//
		//	描画
		//
		////////////////////////////////

		// カーソルを非表示にする
		Cursor::RequestStyle(CursorStyle::Hidden);

		// すべてのブロックを描画する
		for (const auto& brick : bricks)
		{
			brick.stretched(-1) // 1 px 縮ませることで境界線をわかりやすくする
				.draw(HSV{ brick.y - 40 }); // Y 座標に応じて色を変える
		}

		// ボールを描く
		ball.draw();

		// パドルを描く
		paddle.rounded(3) // 角を少し丸くする
			.draw();
	}
}
```
