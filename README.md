# Clavier ZMK Firmware

左右分割・トラックボール付きカスタムキーボードのZMKファームウェア設定です。

## ハードウェア構成

| 項目 | 内容 |
|------|------|
| マイコン (左右共通) | Seeed Studio XIAO nRF52840 |
| キー数 | 左: 4行×5列 (20キー) / 右: 4行×6列 (24キー) / 合計44キー |
| トラックボール | PMW3610 (左手側のみ) |
| 接続方式 | BLE 無線スプリット + USB (左側) |

## ピンアサイン

### 左手側 (Central) - 20キー

| 信号 | XIAO端子 | nRF52840 GPIO |
|------|----------|---------------|
| Row0 | A4 | P0.03 |
| Row1 | A10 | P0.28 |
| Row2 | A11 | P0.29 |
| Row3 | B8_TX | P1.11 |
| Col0 | NFC2 | P0.10 |
| Col1 | B9_RX | P1.12 |
| Col2 | A7_SCK | P1.13 |
| Col3 | A5_MISO | P1.14 |
| Col4 | A6_MOSI | P1.15 |

### 右手側 (Peripheral) - 24キー

| 信号 | XIAO端子 | nRF52840 GPIO |
|------|----------|---------------|
| Row0 | A4 | P0.03 |
| Row1 | A11 | P0.29 |
| Row2 | A9_SCL | P0.05 |
| Row3 | B8_TX | P1.11 |
| Col0 | A6_MOSI | P1.15 |
| Col1 | A5_MISO | P1.14 |
| Col2 | B9_RX | P1.12 |
| Col3 | A7_SCK | P1.13 |
| Col4 | A8_SDA | P0.04 |
| Col5 | A2 | P0.02 |

> 左右でピンアサインが異なります。右手側は左手側のPMW3610用SPI/GPIOピンをキーマトリクスに転用しています。

### PMW3610 ピンアサイン (左手側のみ)

| 信号 | XIAO端子 | nRF52840 GPIO |
|------|----------|---------------|
| SCLK | A9_SCL | P0.05 |
| SDIO | A8_SDA | P0.04 |
| CS | NFC1 | P0.09 |
| MOTION (割り込み) | A2 | P0.02 |

## キーマップ

44キー (左5列 + 右6列) × 4行

### Layer 0: QWERTY (デフォルト)

```
Q    W    E    R    T  |  Y    U    I    O    P    [
A    S    D    F    G  |  H    J    K    L    ;    '
Z    X    C    V    B  |  N    M    ,    .    /    ]
CTRL GUI  ALT  LWR SPC|  SPC  RSE  ENT BSPC  DEL  \
```

### Layer 1: LOWER - `LWR` 長押し

```
1    2    3    4    5  |  6    7    8    9    0    -
!    @    #    $    %  |  ^    &    *    (    )    =
`    ~                 |                           +
```

### Layer 2: RAISE - `RSE` 長押し

```
ESC  F1   F2   F3   F4 |  F5   F6   F7   F8   F9   F10
TAB  F11  F12 INS      | ←    ↓    ↑    →   END  HOME
CAPS BT0  BT1  BT2 CLR | PGDN PGUP           PRTSC
```

### Layer 3: MOUSE (トラックボール操作時に自動有効化)

```
右手ホームポジション: J=右クリック  K=左クリック  L=中クリック
```

## ビルド・フラッシュ方法

### GitHub Actions (推奨)

1. このフォルダを GitHub リポジトリとして公開
2. `.github/workflows/build.yml` が自動生成される
3. Actions タブからアーティファクトをダウンロード

### ローカルビルド

```bash
west init -l config
west update

# 左手側
west build -b seeeduino_xiao_ble -- -DSHIELD=clavier_left -DZMK_CONFIG="${PWD}/config"
cp build/zephyr/zmk.uf2 clavier_left.uf2

# 右手側
west build -b seeeduino_xiao_ble -- -DSHIELD=clavier_right -DZMK_CONFIG="${PWD}/config"
cp build/zephyr/zmk.uf2 clavier_right.uf2
```

### フラッシュ手順

1. XIAO nRF52840 のリセットボタンを**素早く2回押す**
2. `XIAO-SENSE` ドライブがPCに表示される
3. `.uf2` ファイルをドライブにコピー
4. **左手側 → 右手側** の順でフラッシュ

## 使用するZMK Fork

標準ZMKにはPMW3610ドライバが含まれていないため、[urob's ZMK fork](https://github.com/urob/zmk) を使用します (`west.yml` で指定済み)。
