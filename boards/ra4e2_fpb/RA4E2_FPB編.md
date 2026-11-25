<!--
  手順書テンプレート(全ボード共通)
  - このファイルをコピーして、ボードごとのフォルダに保存してから書き始める。
  - 章の番号と名前は変えない。該当しない章は消さずに「本ボードでは作成しない(理由:〜)」と書く。
  - ボード独自の内容は、第9章以降に追加章として書く。
  - このコメントはプレビューには表示されない。消さずに残してよい。
-->

# 【ボード名】開発手順書

| 項目 | 内容 |
|---|---|
| 対象ボード | |
| 開発言語 | |
| IDE・ツール | (名称とバージョン) |
| SDK・ライブラリ | (名称とバージョン) |
| 開発PCのOS | (例:Windows 11 24H2) |
| 最終検証日 | (yyyy/mm/dd) |

## 改版履歴

| 版 | 日付 | 担当者 | 内容 |
|---|---|---|---|
| 0.1 | (yyyy/mm/dd) | | 新規作成 |

---

## 0. 概要

### 0.1 この手順書でできること
<!-- 読者がこの手順書を最後まで進めると何ができるようになるかを書く -->

### 0.2 必要な機材

| 品名 | 数量 | 備考 |
|---|---|---|
| | | |

### 0.3 前提知識・事前準備
<!-- 既存の Git/GitHub 運用・Redmine 運用を参考に、前提とする知識と準備を書く -->

---

## 1. 開発環境構築

### 1.1 ツールのインストール
### 1.2 ドライバのインストール
### 1.3 インストールできたことの確認

## 2. シリアルターミナルの使い方

### 2.1 使用するツールと接続方法
### 2.2 通信設定(ポート、ボーレートなど)
### 2.3 動作確認

## 3. プロジェクト作成

### 3.1 新規プロジェクトの作成
### 3.2 プロジェクトの構成(フォルダ・ファイルの役割)

## 4. コーディング・ビルド・書込み(RTOSなし)

<!-- 各題材の節は「作るもの → 配線 → プログラム → ビルドと書込み → 動作確認」の順で書く -->

### 4.1 題材1:LED点滅
### 4.2 題材2:ボタン入力
### 4.3 題材3:UART出力
### 4.4 題材4:タイマ割込み

## 5. コーディング・ビルド・書込み(FreeRTOS)
この章では、 FreeRTOS を導入した題材を実装する。
### 5.1 FreeRTOSの導入・設定
この節では、 FreeRTOS を導入したプロジェクトを作成する。
1. プロジェクト作成ウィザードの[Build Artifact and RTOS Selection]ページにて、[RTOS Selection]の項目を[FreeRTOS]に設定する。
2. その他の項目を新規プロジェクトの作成と同じに設定する。
3. プロジェクト作成ウィザードを終了する。
> 確認：生成された configuration.xml ファイルをクリックし、 [Stacks]タブに[FreeRTOS Port]の Stack を確認できれば成功。

### 5.2 題材5:複数タスク
この節では、 LED を点滅させるタスクとボタンを押した回数を数えるタスクを同時に動かすプログラムを FreeRTOS で実装する。  
なお、本ボードでは配線の必要はない。  
1. [プロジェクトエクスプローラー]ビューから[configuration.xml]をダブルクリックすることで選択する。
2. [Pins]タブにてLED1が接続されているピン(P207)の[Mode]項目を[Output Mode (Initial Low)]に設定する。
3. [Stacks]タブにて[Threads]ペインのツリーから[New Thread]を選択する。
4. [Treads]ペインの[Remove]ボタンを押下することで[New Thread]を削除する。
5. [Treads]ペインの[New Thread]ボタンを押下することでスレッドを生成する。
6. [プロパティー]タブにて[Tread]の[Symbol]と[Name]を次のように設定する。
   | 項目 | 内容 |
   | -- | -- |
   | Symbol | sw_thread |
   | Name | SW Thread |
7. [Treads]ペインの[New Thread]ボタンを押下することでスレッドを生成する。
8. [プロパティー]タブにて[Tread]の[Symbol]と[Name]を次のように設定する。
   | 項目 | 内容 |
   | -- | -- |
   | Symbol | led_thread |
   | Name | LED Thread |
9.  [Generate Project Content]ボタンを押下することでファイルを生成する。
10. [プロジェクトエクスプローラー]ビューから[プロジェクト名\src]下の[sw_thread_entry.c]をダブルクリックすることで選択する。
11. エディターにて[sw_thread_entry.c]ファイルの冒頭に次のコードを記入する。
    ```c
    #include <stdio.h>
    ```
12. エディターにて[sw_thread_entry.c]ファイルの[sw_thread_entry]関数内に次のコードを記入する。
    ```c
    FSP_PARAMETER_NOT_USED (pvParameters);

    bsp_io_level_t sw_level;
    bsp_io_level_t previous_sw1_level = BSP_IO_LEVEL_HIGH;
    uint16_t sw1_press_count = 0;
    char sw1_press_count_str[8];

    R_SCI_UART_Open(&g_uart0_ctrl, &g_uart0_cfg);

    while(1){
        // SW1のピン状態を取得する
        R_IOPORT_PinRead(&g_ioport_ctrl, SW1, &sw_level);

        // SW1のピン状態が「押されていない」状態から「押されている」状態に変わったときにスイッチが押された回数を出力する
        if (sw_level == BSP_IO_LEVEL_LOW)
        {
            if (previous_sw1_level == BSP_IO_LEVEL_HIGH)
            {
                snprintf(sw1_press_count_str,sizeof(sw1_press_count_str),"%d\r\n",++sw1_press_count);
                R_SCI_UART_Write(&g_uart0_ctrl, (const uint8_t *)sw1_press_count_str, strlen(sw1_press_count_str));
            }
        }

        // SW1のピン状態を直前の状態として保存
        previous_sw1_level = sw_level;

        // 10msタスクを待ち状態にする
        vTaskDelay(pdMS_TO_TICKS(10));
    }
    ```
13. [プロジェクトエクスプローラー]ビューから[プロジェクト名\src]下の[led_thread_entry.c]をダブルクリックすることで選択する。
14. エディターにて[led_thread.entry.c]ファイルに次のコードを記入する。
    ```c
    FSP_PARAMETER_NOT_USED (pvParameters);

    bsp_io_level_t led_state = BSP_IO_LEVEL_LOW;

    while (1)
    {
        // LEDのON,OFFを切り替える
        R_IOPORT_PinWrite(&g_ioport_ctrl, LED1, led_state ^= 1);

        // 500msタスクを待ち状態にする
        vTaskDelay (pdMS_TO_TICKS(500));
    }
    ```
15. 上部リボンから[プロジェクト(P)] > [プロジェクトのビルド(B)]を選択することでプロジェクトをビルドする。
16. 上部リボンから[実行(R)] > [デバッグの構成(B)…]を選択することでデバッグの構成ウィザードを開く。
17. デバッグの構成ウィザードのツリーから現在のプロジェクトを選択する。
18. デバッグの構成ウィザード下部の[デバッグ(D)]を押下することで、コードの書き込みとデバッグを開始する。
 
> 確認：デバッグを続け、マイコンボードが期待通りに動作すれば成功。

### 5.3 題材6:タスク間通信
この節では、LEDの点滅速度をボタンを押して切り替えるプログラムを実装する。  
なお、本ボードでは配線の必要はない。  
1. [プロジェクトエクスプローラー]ビューから[configuration.xml]をダブルクリックすることで選択する。
2. [Pins]タブにてLED1が接続されているピン(P207)の[Mode]項目を[Output Mode (Initial Low)]に設定する。
3. [Stacks]タブにて[Threads]ペインのツリーから[New Thread]を選択する。
4. [Treads]ペインの[Remove]ボタンを押下することで[New Thread]を削除する。
5. [Treads]ペインの[New Thread]ボタンを押下することでスレッドを生成する。
6. [プロパティー]タブにて[Tread]の[Symbol]と[Name]を次のように設定する。
   | 項目 | 内容 |
   | -- | -- |
   | Symbol | sw_thread |
   | Name | SW Thread |
7. [Treads]ペインの[New Thread]ボタンを押下することでスレッドを生成する。
8. [プロパティー]タブにて[Tread]の[Symbol]と[Name]を次のように設定する。
   | 項目 | 内容 |
   | -- | -- |
   | Symbol | led_thread |
   | Name | LED Thread |
9.  [Generate Project Content]ボタンを押下することでファイルを生成する。
10. [プロジェクトエクスプローラー]ビューから[プロジェクト名\src]下の[sw_thread_entry.c]をダブルクリックすることで選択する。
12. エディターにて[sw_thread.entry.c]ファイルの[sw_thread_entry]関数内に次のコードを記入する。
    ```c
    FSP_PARAMETER_NOT_USED (pvParameters);

    bsp_io_level_t sw_level;
    bsp_io_level_t previous_sw1_level = BSP_IO_LEVEL_HIGH;
    uint8_t blink_mode = 0;

    while (1)
    {
        // SW1のピン状態を取得する
        R_IOPORT_PinRead(&g_ioport_ctrl, SW1, &sw_level);

        // SW1のピン状態が「押されていない」状態から「押されている」状態に変わったときに点滅モードを切り替える
        if (sw_level == BSP_IO_LEVEL_LOW)
        {
            if (previous_sw1_level == BSP_IO_LEVEL_HIGH)
            {
                blink_mode ^= 1;
            }
        }

        // キューに点滅モード状態を渡す
        xQueueOverwrite(g_sw_queue, &blink_mode);

        // SW1のピン状態を直前の状態として保存
        previous_sw1_level = sw_level;

        // 10msタスクを待ち状態にする
        vTaskDelay (pdMS_TO_TICKS(10));
    }
    ```
13. [プロジェクトエクスプローラー]ビューから[プロジェクト名\src]下の[led_thread_entry.c]をダブルクリックすることで選択する。
14. エディターにて[led_thread.entry.c]ファイルの[led_thread_entry]関数内に次のコードを記入する。
    ```c
    FSP_PARAMETER_NOT_USED (pvParameters);

    uint8_t led_state = BSP_IO_LEVEL_LOW;
    uint8_t blink_mode = 0;
    uint16_t wait_time = 50;
    uint16_t loop_count = wait_time;

    while (1)
    {
        // キューから取得した点滅モード状態によってwait_timeを変更する
        if (xQueueReceive(g_sw_queue, &blink_mode, portMAX_DELAY)
            == pdPASS)
        {
            wait_time = (blink_mode == 1 ? 10 : 50);
        }

        // loop_countが0になるたびにLEDのON,OFFを切り替える
        if (--loop_count <= 0)
        {
            R_IOPORT_PinWrite(&g_ioport_ctrl, LED1, led_state ^= 1);
            loop_count = wait_time;
        }
    }
    ```
15. 上部リボンから[プロジェクト(P)] > [プロジェクトのビルド(B)]を選択することでプロジェクトをビルドする。
16. 上部リボンから[実行(R)] > [デバッグの構成(B)…]を選択することでデバッグの構成ウィザードを開く。
17. デバッグの構成ウィザードのツリーから現在のプロジェクトを選択する。
18. デバッグの構成ウィザード下部の[デバッグ(D)]を押下することで、コードの書き込みとデバッグを開始する。
 
> 確認：デバッグを続け、マイコンボードが期待通りに動作すれば成功。

## 6. デバッグ

### 6.1 デバッガの接続(配線図・写真)
### 6.2 デバッグの開始
### 6.3 ブレークポイント・ステップ実行
### 6.4 変数・メモリ・レジスタの確認

## 7. トラブルシューティング

| 症状 | 原因 | 対処 |
|---|---|---|
| | | |

## 8. 参考資料

- 

<!-- ボード独自の追加章がある場合は、ここから「## 9. 〜」として追加する -->

<!--
  記述ルール(書きぶりを全章でそろえる)
  1. 見出し:章は「## 1. 開発環境構築」、節は「### 1.1 ツールのインストール」の形にする。
  2. 手順:番号付きリストで書き、1つの番号に1つの操作。文末は「〜する。」にそろえる。
  3. 確認:手順のあとに「> 確認:〜れば成功」と、成功したかどうかの判断方法を書く。
  4. 注意:まちがえやすい所は「> 注意:〜」と書く。
  5. 画像:画面操作にはスクリーンショットを付ける。images/ に置き、名前は担当番号から始める(例:p1_ext.png)。
  6. 図:処理の流れやタスクの関係は Mermaid で書く。配線は図か写真で、ピン番号と信号名を書く。
  7. プログラム:手順書の中にコードブロックで書き、日本語のコメントを付ける。
  8. バージョン:ツールと SDK のバージョンを書く。
  9. 用語:同じものは同じことばで書く。英数字は半角で、日本語との間に半角スペースを入れる。
  10. つまずいた点:第7章に必ず記録する。
-->
