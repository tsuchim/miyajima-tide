# Miyajima Tide Library (Itsukushima Tide)

A lightweight JavaScript library for calculating tidal levels around the Great Torii Gate of Itsukushima Shrine in Miyajima, Hiroshima, Japan.

This library runs entirely in the browser and does not require any server-side processing, making it suitable for integration into a wide range of applications such as websites, exhibitions, tourism tools, and educational projects.

---

## Purpose

The Great Torii Gate of Itsukushima Shrine stands in the coastal waters of **Ōno Seto**, a strait adjacent to Miyajima Island.  
Its appearance changes dramatically with the tide: at low tide it can be approached on foot, while at high tide it appears to float on the sea.

The purpose of this library is to:

- Calculate tidal levels around the Torii Gate area
- Use textbook-based tidal harmonic analysis and published tidal constants
- Perform all calculations in JavaScript
- Provide a reusable and embeddable component for various applications

---

## Background

This project originated from an encounter at a **Hiroshima Prefecture–related competition**, where the idea of using technology to enhance the experience of Miyajima was discussed.

At that time, the implementation was not feasible.  
This library is the result of revisiting that idea and developing it as a reusable technical foundation for applications related to Miyajima.

---

## Features

- Fully client-side, serverless operation
- No backend or API required
- Based on standard tidal harmonic calculation formulas
- Includes tidal constants specifically for Itsukushima (Miyajima)
- Suitable for commercial and non-commercial use

---

## Demo

A demo application is available via **GitHub Pages**.  
Using GitHub Pages is **not required** to use this library; it is provided only as a convenient demonstration.

(Replace the URL below with your actual Pages URL.)

```

https://<username>.github.io/<repository>/

````


## Time Handling

- **Library (tide-lib) API is UTC-only.** All public methods accept `Date` values that represent a **UTC instant** and the core logic is computed strictly in UTC.
- **No implicit local time / JST conversion is performed inside the library.**
- **GitHub Pages demo uses JST for convenience.** The demo UI treats user input as **JST** and performs an explicit **JST → UTC** conversion before calling the library.

If you need to convert a JST wall-clock time to UTC in your own app, do it explicitly (same approach as the demo):

```js
// "YYYY-MM-DDTHH:mm" (interpreted as JST) -> Date (UTC instant)
function parseDatetimeLocalAsJSTToUTC(v) {
  const [datePart, timePart] = String(v).split("T");
  const [y, m, d] = datePart.split("-").map(Number);
  const [hh, mi] = timePart.split(":").map(Number);
  return new Date(Date.UTC(y, m - 1, d, hh - 9, mi, 0, 0));
}
```

## Usage (Overview)

```js
import { MiyajimaTide } from "./tide-lib.js";

const tide = new MiyajimaTide();
const heightCm = tide.heightCmAtUTC(new Date());

console.log(heightCm);
````

See `index.html` for a complete example.

---

## License

MIT License

This software may be used, modified, and distributed freely, including in commercial applications.

---

## Message

Miyajima is a place where nature, history, and culture are closely intertwined.
It is our hope that this library will serve as a foundation for applications, visualizations, research, and creative projects related to Miyajima, and help encourage wider use and appreciation of the area.

Contributions and reuse are welcome.


## デモ

[宮島潮位計算アプリ](https://tsuchim.github.io/miyajima-tide/) で、リアルタイムで潮位を確認できます。

---

## インストール

### ブラウザでの使用

```html
<script type="module">
  import { MiyajimaTide } from "https://raw.githubusercontent.com/tsuchim/miyajima-tide/main/tide-lib.js";
  
  const tide = new MiyajimaTide();
    const height = tide.heightCmAtUTC(new Date());
  console.log(`潮位: ${height.toFixed(1)} cm`);
</script>
```

### ローカルでの使用

```bash
git clone https://github.com/tsuchim/miyajima-tide.git
cd miyajima-tide
```

その後、`index.html` をブラウザで開くか、ローカルサーバで実行してください。

```bash
# Python 3
python -m http.server 8000

# Node.js
npx http-server
```

---

## API リファレンス

### `MiyajimaTide` クラス

#### コンストラクタ

```js
const tide = new MiyajimaTide(params);
```

- `params` (optional): 調和定数パラメータオブジェクト（デフォルト: 厳島の定数）

#### メソッド

##### `heightCmAtUTC(dateUtc: Date): number`

指定の日時(UTC)における潮位を計算します。

```js
const height = tide.heightCmAtUTC(new Date());
console.log(height); // cm単位の潮位
```

##### `seriesCmAtUTC(startDateUtc: Date, minutes: number, stepMinutes?: number): Array`

指定の期間(UTC)の潮位系列を計算します。戻り値の各要素は `{ tUTC: Date, cm: number }` です。

```js
const series = tide.seriesCmAtUTC(new Date(), 24 * 60, 10);
// 今から24時間、10分刻みで潮位を計算
series.forEach(p => {
  console.log(`${p.tUTC.toISOString()}: ${p.cm.toFixed(1)} cm`);
});
```

---

## 技術詳細

このライブラリは、以下の教科書に基づいています：

- **調和解析の基礎**: 潮汐の合成式 η(t) = Z0 + Σ f_i H_i cos([V_i+u_i]-κ_i)
- **天文引数の近似式**: s, h, p, N の計算（式(27)-(30)）
- **時間変化**: T, s, h, p の時間進行式
- **節補正係数**: f, u の計算（第2表より）

**調和定数（厳島・Itsukushima）**

| 分潮 | H (cm) | κ (°) | 説明 |
|------|--------|-------|------|
| O1   | 24.0   | 201.0 | 主太陰日周潮 |
| P1   | 10.3   | 219.0 | 主太陽日周潮 |
| K1   | 31.0   | 219.0 | 日月合成日周潮 |
| M2   | 103.0  | 277.0 | 主太陰半日周潮 |
| S2   | 40.0   | 310.0 | 主太陽半日周潮 |
| K2   | 10.9   | 310.0 | 日月合成半日潮 |

---

## ライセンス

このプロジェクトは [MIT License](LICENSE) の下で公開されています。

商用・非商用を問わず、自由に使用、修正、配布が可能です。

---

## 貢献

バグ報告、機能提案、プルリクエストなどは、[GitHub Issues](https://github.com/tsuchim/miyajima-tide/issues) でお受けしています。

---

## 著者

- tsuchim

---

## 参考資料

- 潮汐調和解析に関する教科書・論文
- 国土地理院の調査データ
- 厳島神社周辺の海洋観測データ