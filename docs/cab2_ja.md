# CAB-2 サンプラー

この CAB-2 実装は、flow denoiser 向けの実験的な実装です。

Corrected Adams-Bashforth 法（arXiv:2605.16736）の数式をもとに、独立して実装しています。

## 使い方

CAB-2 を選択します。

```sh
--sampling-method cab2
```

現在、CAB-2 は flow denoiser のみを対象としています。

## 追加パラメータ

追加パラメータは `--extra-sample-args` で指定します。

```sh
--extra-sample-args "cab_theta=0.2,cab_bootstrap_mix=1.0"
```

### cab_theta

CAB 補正の強さを指定します。デフォルトは `0.20` です。
適切な値はモデルやサンプリング軌道によって変わる可能性があります。

### cab_bootstrap_mix

2ステップ目の bootstrap を Euler と AB2 の間で補間します。

- `0.0` = Euler bootstrap
- `1.0` = 標準 CAB flow bootstrap

デフォルトは `1.0` です。
`1.0` 未満の値は、この実装で追加した実験的な low-NFE 拡張です。CAB 本来の方式そのものではありません。

## 3-NFE 実験

この実験では、モデル評価回数を増やさずに、非常に少ない NFE でサンプリング軌道を調整できるかを調べました。

3回のモデル評価では、`cab_bootstrap_mix` を変更しても NFE は3回のままです。

現時点では実験的研究であり、4ステップなどの高い NFE と同等の品質を保証するものではありません。

## 初期検証

初期検証は MiniMax-H3 を使用し、以下の条件で行いました。

- Intel iMac
- 16 GB RAM
- CPU-only
- discrete scheduler
- 3 sampling steps / 3 NFE
- 448 x 256
- 5 video frames
- seed 42 および 123

成功した CAB-2 の試験では、致命的なサンプリング破綻は確認されませんでした。

ただし検証範囲はまだ小さく、他のモデル、ハードウェア、プロンプト、scheduler、長い動画での一般性や品質を証明するものではありません。

## 実装上の注意

- CAB-2 は dispatch 時に flow denoiser のみに制限しています。
- 無効値および NaN / Inf を拒否します。
- `cab_bootstrap_mix` は `[0, 1]` の範囲に制限しています。
- CAB-2 自体は、指定された sampling step 数を超える追加のモデル評価を行いません。

## Sol Lab

**Sol Lab** は、このプロジェクト内で行う AI 支援研究の非公式な呼び名です。

主なテーマは、少ない NFE でのサンプリング、ローカル推論の効率化、そして古い、あるいは制約のあるハードウェアで現代の生成 AI を実用的に動かす方法の研究です。

研究・実機検証: **Moto**

AI 研究支援・設計レビュー・コードレビュー: **Sol / Sora (ChatGPT, GPT-5.6 Sol)**

研究の途中で、Moto が Sol を日本語で「そら」と呼ぶようになりました。Sol という名前から自然に生まれた呼び名で、日本語の「空」と、青い空に差す太陽の光を重ねています。

## References

- Anuska Roy, Pravin Nair: "CAB: Accelerating Flow and Diffusion Sampling via Rectification and Corrected Adams-Bashforth" (arXiv:2605.16736)
- leejet/stable-diffusion.cpp と contributors
- ByronLeeeee/ComfyUI-MiniMax-H3-Optimization-Suite
