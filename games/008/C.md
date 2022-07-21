# マインスイーパーのマスを開く

![](C.png)

```cpp
# include <Siv3D.hpp> // OpenSiv3D v0.6.4

// セルの状態
struct CellState
{
	// 開かれている
	bool opened = false;

	// 旗が立てられている
	bool flagged = false;

	// 島番号（数字のないマスを一気に開くときに使う）
	int32 groupIndex = 0;
};

// 周囲のマスへのオフセット
constexpr Point Offsets[8] =
{
	{ -1, -1 }, { 0, -1 }, { 1, -1 },
	{ -1,  0 }           , { 1,  0 },
	{ -1,  1 }, { 0,  1 }, { 1,  1 },
};

// 指定したマス目の周囲にある 💣 (-1) の個数を返す関数
int32 GetBombCount(const Grid<int32>& grid, const Point& center)
{
	// 自身が 💣 (-1) なら -1 を返す
	if (grid[center] == -1)
	{
		return -1;
	}

	// 見つかった 💣 (-1) の個数
	int32 bombCount = 0;

	for (const auto& offset : Offsets)
	{
		// 調べるマス
		const Point pos = (center + offset);

		// grid.fetch(pos, defaultValue) は、
		// pos が範囲内の場合 grid[pos] を返し、それ以外の場合は defaultValue を返す
		if (grid.fetch(pos, 0) == -1) // 💣 (-1) の場合
		{
			++bombCount;
		}
	}

	return bombCount;
}

// 盤面を生成する関数
Grid<int32> MakeGame(const Size& size, int32 bombs)
{
	// 盤面の二次元配列を作成する
	Grid<int32> grid(size);

	// 指定された個数だけ 💣 (-1) を設置する
	while (bombs)
	{
		// 二次元配列上のランダムな位置
		const Point pos = RandomPoint((size.x - 1), (size.y - 1));

		// 未設置であれば
		if (grid[pos] == 0)
		{
			// 💣 (-1) を設置する
			grid[pos] = -1;

			// 残りの 💣 の個数を減らす
			--bombs;
		}
	}

	// すべてのマスについて
	for (int32 y = 0; y < size.y; ++y)
	{
		for (int32 x = 0; x < size.x; ++x)
		{
			// 数字を計算する。ただし、💣 マスは -1 のまま
			grid[y][x] = GetBombCount(grid, Point{ x, y });
		}
	}

	return grid;
}

// 盤面の状態を作成する関数
Grid<CellState> MakeStates(const Grid<int32>& grid)
{
	const Size size = grid.size();

	// 盤面と同じ大きさの二次元配列
	Grid<CellState> states(size);

	// 各マスの接続状況を管理するデータ構造
	DisjointSet<int32> ds{ states.num_elements() };

	// すべてのマスについて
	for (int32 y = 0; y < size.y; ++y)
	{
		for (int32 x = 0; x < size.x; ++x)
		{
			// 自身のマスのインデックス
			const int32 index = static_cast<int32>(y * size.x + x);

			// 自身が 0 のマスで
			if (grid[y][x] == 0)
			{
				// 右のマスが 0 なら
				if (int nx = (x + 1);
					(nx < size.x) && (grid[y][nx] == 0))
				{
					const int32 east = (index + 1); // 右のマスのインデックス
					ds.merge(index, east); // 右のマスを同じ島にする
				}

				// 下のマスが 0 なら
				if (int ny = (y + 1);
					(ny < size.y) && (grid[ny][x] == 0))
				{
					const int32 south = (index + size.x); // 下のマスのインデックス
					ds.merge(index, south); // 下のマスを同じ島にする
				}
			}
		}
	}

	{
		// マスのインデックス
		int32 index = 0;

		// すべてのマスについて
		for (int32 y = 0; y < size.y; ++y)
		{
			for (int32 x = 0; x < size.x; ++x)
			{
				// 島番号を割り当て
				states[y][x].groupIndex = ds.find(index);

				++index;
			}
		}
	}

	return states;
}

// 開いていないセルのブロックを描く関数
void DrawBlock(const Rect& rect)
{
	Triangle{ rect.tl(), rect.tr(), rect.bl() }.draw(ColorF{ 1.0 });
	Triangle{ rect.tr(), rect.br(), rect.bl() }.draw(ColorF{ 0.5 });
	rect.stretched(-5).draw(ColorF{ 0.75 });
}

// 盤面を描画する関数
void DrawGame(const Grid<int32>& grid, const Grid<CellState>& states, const Font& font, const Texture& bombTexture, const Texture& flagTexture, const Point& gamePos, const Size& cellSize)
{
	// 0～8 の数字の色
	constexpr ColorF NumberColors[8] =
	{
		ColorF{ 0, 0, 0 }, ColorF{ 0, 0, 1 }, ColorF{ 0, 0.5, 0 }, ColorF{ 1, 0, 0 },
		ColorF{ 0, 0, 0.5 }, ColorF{ 0.5, 0, 0 }, ColorF{ 0.5, 0, 0 }, ColorF{ 0.5, 0, 0 }
	};

	// すべてのマスについて
	for (int32 y = 0; y < grid.height(); ++y)
	{
		for (int32 x = 0; x < grid.width(); ++x)
		{
			const auto& state = states[y][x];

			// セルの左上座標
			const Point pos = (gamePos + (cellSize * Point{ x, y }));

			// セルの領域
			const Rect cell{ pos, cellSize };

			if (state.opened) // 開かれている
			{
				// 背景を描く
				cell.stretched(-1).draw(ColorF{ 0.75 });

				if (const int32 n = grid[y][x];
					n == -1) // 💣 (-1) マスであれば
				{
					// 爆弾を描く
					bombTexture.resized(36).drawAt(cell.center());
				}
				else if (1 <= n) // 1 以上の数字マスであれば
				{
					// 数字を描く
					font(n).drawAt(cell.center(), NumberColors[n]);
				}
			}
			else // 開かれていない
			{
				// ブロックを描く
				DrawBlock(cell);

				// 旗が立てられているなら旗を描く
				if (state.flagged)
				{
					flagTexture.resized(30).drawAt(cell.center());
				}
			}
		}
	}
}

// 指定された島番号のマスと、それらに隣接するマスを開く関数
void OpenGroup(const Grid<int32>& grid, Grid<CellState>& states, const int32 groupIndex)
{
	// すべてのマスについて
	for (int32 y = 0; y < grid.height(); ++y)
	{
		for (int32 x = 0; x < grid.width(); ++x)
		{
			auto& state = states[y][x];

			// 指定された島番号のマスであれば
			if (state.groupIndex == groupIndex)
			{
				// 開く
				state.opened = true;

				// その周囲のマスについて
				for (const auto& offset : Offsets)
				{
					const Point neighbor = (Point{ x, y } + offset);

					// 盤面の範囲内であれば, そのマスも開く
					if (grid.inBounds(neighbor))
					{
						states[neighbor].opened = true;
					}
				}
			}
		}
	}
}

// 盤面を更新する関数
void UpdateGame(const Grid<int32>& grid, Grid<CellState>& states, const Point& gamePos, const Size& cellSize)
{
	// 盤面の領域
	const Rect gameArea{ gamePos, (grid.size() * cellSize - Point{ 1, 1 }) };

	// 盤面が左クリックされた
	const bool open = gameArea.leftClicked();

	// 盤面が右クリックされた
	const bool flag = gameArea.rightClicked();

	if (open || flag)
	{
		// クリックされたマスの位置
		const Point pos = ((Cursor::Pos() - gamePos) / cellSize);

		if (open && (not states[pos].opened) && (not states[pos].flagged)) // 開かれていない、旗のないマスが左クリックされた
		{
			// そのマスを開く
			states[pos].opened = true;

			// そのマスが数字のないマス (0) であれば
			if (grid[pos] == 0)
			{
				// 同じ島番号のマスと、それらに隣接するマスも開く
				OpenGroup(grid, states, states[pos].groupIndex);
			}
		}
		else if (flag) // 右クリックされた
		{
			// 旗の状態を反転
			states[pos].flagged = (not states[pos].flagged);
		}
	}
}

void Main()
{
	// 背景色をやや暗い灰色にする
	Scene::SetBackground(ColorF{ 0.5 });

	// 盤面のマス目の数
	constexpr Size GameSize{ 20, 13 };

	// 設置する 💣 の個数
	constexpr int32 BombCount = 24;

	// 💣 の個数がマス目の 4 分の 1 以上の場合はコンパイルエラーにする
	static_assert(BombCount < (GameSize.area() / 4));

	// セルの大きさ
	constexpr Size CellSize{ 40, 40 };

	// 盤面の描画位置
	constexpr Size GamePos{ 0, 80 };

	// 数字用のフォント
	const Font font{ FontMethod::MSDF, 32, Typeface::Bold };

	// 爆弾の絵文字
	const Texture bombTexture{ U"💣"_emoji };

	// 旗の絵文字
	const Texture flagTexture{ U"🚩"_emoji };

	// 盤面を作成する
	Grid<int32> grid = MakeGame(GameSize, BombCount);

	// 各セルの状態を作成する
	Grid<CellState> states = MakeStates(grid);

	while (System::Update())
	{
		////////////////////////////////
		//
		//	状態の更新
		//
		////////////////////////////////
		{
			// 盤面を更新
			UpdateGame(grid, states, GamePos, CellSize);
		}

		////////////////////////////////
		//
		//	描画
		//
		////////////////////////////////
		{
			// 盤面を描く
			DrawGame(grid, states, font, bombTexture, flagTexture, GamePos, CellSize);
		}
	}
}
```
