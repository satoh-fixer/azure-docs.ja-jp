---
title: Project Personality Chat とは
titlesuffix: Azure Cognitive Services
description: この記事では、ボットの会話機能を強化するクラウド ベースの API、Azure Project Personality Chat の概要を示します。
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: overview
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ROBOTS: NOINDEX
ms.openlocfilehash: d6b632d6d4c0762ffd20aaf57a329095e853915c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704262"
---
# <a name="what-is-project-personality-chat"></a>Project Personality Chat とは

Project Personality Chat は、選択された異なる性格に一致する短い会話を処理することにより、ボットの会話能力を強化します。 Personality Chat は、意図分類器を使用して一般的な短い会話の意図を識別し、性格に応じた応答を生成します。 ボットは、意図と信頼スコアに基づき、精選された編集ベースから最適な応答を選択するか、ディープ ニューラル ネットワーク (DNN) を使用してリアルタイムに応答を生成します。 ペルソナは、3 つの既定から選択できます。 ペルソナのモデルは、選択された性格に最も近い応答を返します。

一般的な短い会話によるクエリ用のカスタマイズ可能な編集リストが使用できます。 Microsoft Bot Framework SDK を使用すると、これを簡単に統合することができます。 この SDK を使うと、これらを最初から記述するコストや時間を節約できます。

## <a name="getting-started-with-project-personality-chat"></a>Project Personality Chat スタート ガイド

Project Personality Chat ラボ ページと使用可能なデモとのチャットをご覧ください。また、サービスが使用可能な場合は、早期アクセスを要求できます。
現在は、Microsoft Bot Framework SDK を使用して、ボットにカスタマイズ可能な編集専用のライブラリを統合することもできます。 <br>
[サンプル:Personality Chat をボットに統合する](https://github.com/Microsoft/BotBuilder-PersonalityChat/) <br>
[Personality Chat ライブラリを試してみる](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp)

## <a name="generating-responses-using-neural-networks"></a>ニューラル ネットワークを使用して応答を生成する

Personality Chat は、ディープ ラーニング モデルを使用して、一般的な会話パターンの学習や応答の生成を行います。 応答生成のモデルは、再帰型ニューラル ネットワーク (RNN) です。 このモデルは、何百万もの会話でトレーニングを行い、人間の対話パターンを理解、学習します。 学習した文構造パターンを使用して、新しいクエリや応答を生成します。 この新しい応答を生成するとき、モデルは単語の「文字ボキャブラリ」を検索し、応答に最適な n 個の最初の単語を選択します。 続いて、生成された最初の単語のそれぞれについて、ビーム検索を使用して最適な n 個の 2 つ目の単語を検索します。 モデルが「文末記号」(EOS) を選択すると、応答が完了したと見なされます。 すべての応答がそろったら、完全な応答の確率スコアに基づき、n 個の最適な応答を選択します。

モデルは、適切な「構造」を持つ文脈的に正しい順番を生成することを学習します。 たとえば、「今話したいですか?」のような質問について考えれば、 妥当な応答の構造について理解することができます。応答は、おそらく「はい」や「いいえ」で言い換えられる言葉から始まり、代名詞は「私」である可能性が高いでしょう。 応答が「いいえ」である場合は、丁寧な説明が続く可能性が高い、といったことが考えられます。

システムは、会話の基本構造を表す言語モデルを学習しようとします。 この構造では、トピック、性格などの外部的制約が応答の一部に影響するようになっている必要があります。  こういった制約は多様なパターンから学習するので、これらは短い会話を行うアプリケーションに適しています (ただし、それに限定されません)。

![応答生成の Sequence to Sequence モデル](./media/overview/sequence-to-sequence-model.png)

## <a name="personality-modeling"></a>性格のモデリング

 データに基づく会話モデルで難しいのは、一貫性のある「性格」を与えることです。 ペルソナは、会話のやり取りの際の特徴として定義されます。 ペルソナは、アイデンティティ、言語行動、およびインタラクション スタイルの各要素で構成されたものと見ることができます。 現在のバージョンの Personality Chat では、言語行動とインタラクション スタイルに重点が置かれています。

このモデルは、個々の話者をベクトルまたは埋め込みとして表現します。 これは、応答の内容やスタイルに影響を与える話者の情報をエンコードしたものです。 モデルは、異なる話者から提供された会話の内容に基づいて、話者表現を学習します。 似たような応答を行う話者は、ベクトル空間内で近い位置にある似たような埋め込みを持つ傾向にあります。 これにより、ベクトル空間で近い位置にある話者のトレーニング データから、話者モデルを汎用化することができます。 モデルは、「ペルソナ ID」を使ってベクトル空間で似たような表現を持つ話者のペルソナ クラスターを形成することで、効率的に話者をクラスター化します。

既定のペルソナ用に、属性と精選された応答を使って最も一致度が高い話者クラスターを検索します。 このクラスターが、それぞれの既定の個性のペルソナ ID として選択されます。 性格の種類にかかわらず、ボットとブランド応答のセットを取得することにより、継続的なカスタマイズを行うこともできます。 やがて、言語的な応答動作、主要な特徴といった個々のペルソナを、会話で正確にエミュレートできるようになります。

![話者クラスタを使用したペルソナのモデリング](./media/overview/persona-modeling.png)

## <a name="small-talk-intent-understanding"></a>短い会話の意図を解釈する

短い会話の意図に対して確実に応答できるように、Personality Chat には意図分類器があります。 この分類器がタスクや情報クエリを妨害することはありません。 高レベルのチャット分類器は、クエリの意図が短い会話なのか、単なるおしゃべりなのかを判断します。 これは、構文ベースの分類器とセマンティック ベースの分類器によって行われ、そのスコアを組み合わせて判断します。 意図のトレーニングは、会話データ (正の意図のサンプル) と検索およびタスクのクエリ (負の意図のサンプル) を使用して行われます。

その他のサブ意図分類子は、短い会話のサブ分類を識別する際に使用されます。これは、あいさつ、苦情、意見など、サービスが応答する短い会話の種類を制限するために使用されます。 こういったディープ ラーニング分類子は、畳み込み深層構造セマンティック モデル (CDSSM) を使って、すべてのクエリをベクトルに変換します。 トレーニングには、サブ意図用に精選された数万のクエリを使用します。 その後、分類子は、ベクトル空間で最も近い一致を検索することで、クエリと既存の識別済み意図クラスのマッチングを行います。

好ましくない応答を回避できるようにいくつかのコントロールが組み込まれており、それらは、Zo などのインテリジェントなエージェントの知識の上に構築されています。 既定では、Project Personality Chat は、認識したユーザー意図にのみ応答するように設定されています。 Project Personality Chat が状況に適しているかどうかをテストすることができます。 さらにトレーニングが必要なものがある場合は、フィードバックをお送りください。
