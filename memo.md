# アイデアメモ
- [×]文章の特徴量をbertにオンカットして学習
    - 文章の読みやすさを数値化したものはいくつか用意されているらしい
    - 音節数はライブラリある（https://pypi.org/project/syllables/#files）
    - bertの学習ではダメだったけど、スタッキングで入れたらうまくいくかも
    - tf-idfくっつけて学習

- [×]mlm
- adversal training
    - [◯]awp(https://www.kaggle.com/competitions/feedback-prize-english-language-learning/discussion/349594)
    - [◯:0.001]fgm(eps=0.3)

- []headのつけかえ
    - [◯:アンサンブルで使えそう]concat cls token -> これもアンサンブルで効きそう
    - [×]lstm head
    - [×]色々なheadで予測して平均を取る（https://www.kaggle.com/competitions/commonlitreadabilityprize/discussion/260325）

- loss
    - [△]回帰：mseやsmoothl1lossに変更 ->smoothl1lossが良さそう
    - [×]smoothl1lossのbeta=0.025
    - [△]bcewithlogitloss
- []back translation使ったpsuedo label
- 学習パラメータ等の変更
    - [◯:0.03]layerごとにlrの変更（今は0.95で効いてる、baseだと効かない？）
    - [◯:0.03]group化してlr変更(https://github.com/Danielhuxc/CLRP-solution/blob/main/components/optimizer.py)
    - [×]layerの初期化
- [◯]mixout(p=0.2, commonlitでno dropoutと合わせてt0mさんが使ってた)
- [◯]アンサンブル、スタッキング
    - [◯]nelder-mead（これで1つは出す）
    - [◯]gaussian process regression(https://www.kaggle.com/competitions/commonlitreadabilityprize/discussion/258554)
    - [◯]svr(https://www.kaggle.com/code/cdeotte/rapids-svr-cv-0-450-lb-0-44x/notebook)

# 決めたこと
- lr:大きいモデルは1e-5, 小さいモデルは2e-5
- weight decay:大きいモデルは0.1, 小さいモデルは0.01
- scheduler:linear, warmup_rate=0.1
- embeddingをfreezingすると学習が安定してるから使う→fgmとかと相性が良くないっぽい
- AWP:deberta-v3-largeだとeps=1e-2, lr=1e-5（場合によってはlr=2e-4, eps=1e-3が効くかも）
- とりあえずdeberta-familyが強そうだからある程度シングルやったらアンサンブルゲーする
- 使うモデル：deberta-v3, birbird-roberta, funnel, electra, longformer