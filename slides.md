---
title: Coding Agent × 将棋 AI
titleTemplate: '%s'
author: 野田 久順
theme: default
fonts:
  sans: Noto Sans JP
  mono: Space Mono
  provider: none

class: text-center
highlighter: shiki
lineNumbers: false
drawings:
  persist: false
transition: slide-left
mdc: true
wakeLock: false
---

<div class="cover">
  <div class="eyebrow">AI 共創の時代へ - Build with AI Kwansai 2026</div>
  <h1>Coding Agent × 将棋 AI</h1>
  <p class="subtitle">～Coding Agent に将棋 AI を強化してもらった話～</p>
  <div class="meta">
    <div>
      <div>野田 久順</div>
      <div class="muted">ザイオソフト コンピューター将棋サークル</div>
    </div>
    <div class="muted">2026-04-19</div>
  </div>
</div>

---
layout: section
---

# 1. 自己紹介

---
layout: two-cols
---

::left::
## 野田 久順（ひさより）

ザイオソフト コンピューター将棋サークル所属

### 主な実績
- 2017年 第5回将棋電王トーナメント 優勝
- 2021年 CSA貢献賞 受賞
- 2024年 第34回世界コンピュータ将棋選手権 優勝

::right::

![](./public/assets/S0650008697-0024-clipped-Hisayori-Noda.jpg){.intro-photo}
---
layout: center
---

## 今日持ち帰ってほしいこと

1. Coding Agent は「実装」だけでなく「検証・反復」まで強い
2. 強化の鍵は、**依頼の型**と**証拠（再現可能性）**
3. 将棋 AI でも、設計/実験/可視化を自動化すると伸びる

---
layout: two-cols
---

## この発表の流れ

::left::
- 2. 課題（将棋 AI の前提と痛み）
- 3. 方針（Agent採用理由）
- 4. 実行プロセス（指示→実装→検証）

::right::
- 5. 結果（速度・強さ・品質）
- 6. 再現可能な運用ルール
- デモ（できれば）

---
layout: section
---

# 2. 課題

---
layout: two-cols
---

## 将棋 AI とは

::left::
- 将棋を指すソフトウェア
- 局面を入力すると、推奨手と評価値を提示
  - 推奨手: 次に指すべき手
  - 評価値: 先手・後手の有利さを示す数値

::right::

![](./public/assets/2026-02-27-111526.png){.intro-photo}

[将棋ホームページ（参考）](https://sunfish-shogi.github.io/shogihome/)

---
layout: two-cols
---

## ゲーム木

::left::
- 合法手に基づいて、局面遷移を木構造で表したもの
- ゲームの状態遷移を原理的にすべて表現できる
- すべての分岐を調べ切ると、最善手同士の結果を特定できる
- この過程を「ゲームを解く」と呼ぶ

::right::

`mermaid
flowchart TD
  A[開始局面] --> B1[手1]
  A --> B2[手2]
  A --> B3[手3]
  B1 --> C1[応手1]
  B1 --> C2[応手2]
  B2 --> C3[応手1]
  B3 --> C4[応手1]
`
---
layout: two-cols
---

## 探索量から見たゲームの複雑さ

::left::
- ゲームを解くための探索局面数は、複雑さの目安になる
- 探索局面数 ≒ N^M
  - N: 平均合法手数
  - M: 平均終了手数
- 複雑なゲームでは、現実的な時間内に全探索は不可能

::right::

| ゲーム | 探索局面数 |
| --- | --- |
| チェッカー | 10^30 |
| オセロ | 10^60 |
| チェス | 10^120 |
| 中国象棋 | 10^150 |
| 将棋 | 10^220 |
| 囲碁 | 10^360 |

---
layout: two-cols
---

## 探索と評価

::left::
- 一定の手数まで「探索」し、到達局面を「評価」する
  - 探索: 人間の「読み」に相当
  - 評価: 人間の「大局観」に相当

::right::

`mermaid
flowchart TD
  A[現在局面] --> B1[候補手A]
  A --> B2[候補手B]
  A --> B3[候補手C]
  B1 --> C1[評価]
  B2 --> C2[評価]
  B3 --> C3[評価]
`

---
layout: center
---

## CPU エンジンと GPU エンジン

| 項目 | CPUエンジン | GPUエンジン |
| --- | --- | --- |
| 探索アルゴリズム | アルファ・ベータ法 | PUCT |
| 評価関数 | NNUE評価関数 | ディープラーニング評価関数 |
| 探索速度 | 速い | 遅い |
| 評価精度 | 低い | 高い |
| 特異な局面 | 終盤 | 序盤 |
| 主な将棋AI | やねうら王・水匠・tanuki- | dlshogi |

---
layout: center
---

## NNUE 評価関数

- 2018年、那須悠氏により発表
- 主な特徴
  - ディープラーニングによる評価関数
  - CPUでの高速推論
  - 全結合ニューラルネットワーク
  - 活性関数は clipped ReLU
  - 差分計算による効率化
  - 手番の考慮
  - HalfKP 特徴量
  - 整数 SIMD 演算による高速化

---
layout: two-cols
---

## 全結合ニューラルネットワーク

::left::
- 隠れ層 3 層程度の浅いディープラーニング
- 推論速度を重視した、軽量なネットワーク構成

::right::

`mermaid
flowchart LR
  I[入力特徴量] --> H1[全結合層1]
  H1 --> A1[clipped ReLU]
  A1 --> H2[全結合層2]
  H2 --> A2[clipped ReLU]
  A2 --> H3[全結合層3]
  H3 --> O[評価値]
`

---
layout: two-cols
---

## HalfKP 特徴量

::left::
- 将棋盤面を表す数値ベクトル
- 玉の位置と、玉以外の1駒の位置・種類の組を One-hot 化して加算
- 81 × 1548 = 125,388 次元

::right::

![](./public/assets/HalfKP.png){.intro-photo}

玉と玉以外の1駒の組み合わせの例
盤面上に存在するすべての組み合わせを One-hot 化して足し合わせる

---
layout: center
---

## SFNN（Stockfish側のNNUE構成）

- SFNN は、Stockfish で使われる NNUE 系のネットワーク設計
- 2つの視点（手番側 / 相手側）を並列に処理し、最終評価を合成する
- 疎な入力を活かす大きな先頭層と、小さな後段層を組み合わせる
- 低精度整数演算（量子化）を前提に、CPU上で高速に評価できるよう最適化されている

参考: https://official-stockfish.github.io/docs/nnue-pytorch-wiki/docs/nnue.html

---
layout: section
---

# 3. 方針（Agent採用理由）

---
layout: center
---

## なぜ Coding Agent を使うか

- 実装だけでなく検証ループまで自動化できる
- 試行回数を増やし、改善速度を上げられる
- 指示テンプレ化で再現性を確保できる

---
layout: section
---

# 4. 実行プロセス

---
layout: center
---

## 1サイクルの進め方

1. 目的と制約を明示して依頼
2. 実装差分を確認
3. ベンチ・対局・回帰で検証
4. 結果を踏まえて次の改善へ

---
layout: section
---

# 5. 結果

---
layout: center
---

## 結果（速度・強さ・品質）

- 速度: ベンチで改善を確認
- 強さ: 対局結果で改善を確認
- 品質: 回帰テストで破壊的変更を抑制

---
layout: section
---

# 6. 再現可能な運用ルール（まとめ）

---
layout: center
---

## まとめ

- Agent は「計測→改善→回帰」まで自動化すると真価が出る
- 将棋 AI は変更影響が大きいので、検証が最重要
- 指示テンプレを用意して共創の速度を上げる
---
layout: end
---

ありがとうございました


