---
XMPIP: 1
Layer: Applications
Title: Distributed storage bound to Monaparty token
Author: Cryptcoin Junkey <cryptcoin.junkey@gmail.com>
Status: RFC
Type: Standards Track
Created: 2018-02-12
---

# Abstract

P2P 型の分散ストレージシステム IOFS と Monaparty トークンを用いて、
ファイルの公開者がトークンの所有者であることをトラストレスに証明しつつファイルの公開を行うシステムを提案する。

# Motivation

コンテンツの公開者が自分であることを証明する永続的で簡便な手段が求められている。

例えば Monacard は、トークンと画像を紐付けることでカード交換と模した機能を提供している。
Monacard では、事前に SNS 認証と Monacoin 秘密鍵を用いた署名とを組みあせて、コンテンツ公開者とトークン所有者との紐付けを行っていると思われる。
この場合、 Monacard の内部データベースが失われた場合には、コンテンツとトークンの紐付けも失われてしまう。

# 動作レイヤ

本提案の動作レイヤは、Monacoin を Layer1、Monaparty を Layer2 としたとき、Layer3 に属する。
Monaparty を API として動作するアプリケーションとして実現されるものである。

# 実現されるもの

IPFS に記録されたコンテンツの公開者が、
特定の Monaparty トークンの所有者であることを、
Monaparty のブロックチェーンが稼働している限り、
簡便に確認できるシステム。

# 実現されないもの

上記「実現されるもの」に無いものは実現されない。特に、下記の期待には応えられない。

* 暗号化された秘匿性のあるストレージ
* コンテンツの公開者がコンテンツに関する知的財産権を有していることの確認
* 厳密な意味での「忘れられる権利」の保証

# アクターモデル

本提案には、下記のアクターが登場する。

* Owner (コンテンツ公開者)
* Register (コンテンツ登録者)
* Scanner (ブロックチェーン監視者)
* Libraty Reference (問い合わせ応答者)
* Audience (コンテンツ閲覧者)

また、下記の広義のデータベースが登場する

* IPFS
* Monacoin blockchain
* Volatile DB

# Operations by Owner

## Publish

Owner は publish (公開) に際し下記の手順を踏む。

0. 公開コンテンツに対する metadata を作成する。
0. matadata と公開コンテンツを IOFS に追加する。
0. 公開コンテンツと紐付けたい Monaparty トークンを、下記条件でMonacoin ネットワークにブロードキャストする。
 * 送信先 Monacoin アドレスは `MonaStorageVer1xxxxxxxxxxxxxxSidGT` とする。
 * text message として IPFS から得られるハッシュ (`Qm` で始まる32バイト文字列) のみを含める。

送信先の Monacoin アドレスは、秘密鍵が不明であり、送信されたトークンはどこにも再送できない。

## Update

Publish したコンテンツの update (更新) は、publish と同じ手順を踏む。

## Delete

Owner が publish したコンテンツを delete (取り下げ) したい場合には、下記の手順を踏む。

0. 公開コンテンツとの紐付けを解きたい Monaparty トークンを、下記の条件で　Monacoin ネットワークにブロードキャストする。
 * 送信先 Monacoin アドレスは`MonaStorageVer1xxxxxxxxxxxxxxSidGT` とする。
 * text message として　`delete` というASCII文字列のみを含める。

# Register

実運用時には Owner は、技術的なスキルレベルが必ずしも高くないユーザとなることが想定される。
Owner の作業の一部を Register が代行する。Register は、専用のアプリケーションもしくは、ウォレットの拡張機能として実現される。

# Scanner

Scanner は、Monacoin アドレス `MonaStorageVer1xxxxxxxxxxxxxxSidGT` を常時監視し、受信したトークンに添付されたメッセージにより volatile DB の内容を変更する。

* トークンの送信者が同トークンの所有者だった場合、かつメッセージに含まれる文字列が IPFS のハッシュ形式で、そのハッシュを用いて解析可能な `metadata.json`を取得可能なとき、下記の処理を行う。
 * トークン名とハッシュの紐付けを volatile DB に記録する。
 * メタデータの内容に必要に応じ変更を加え volatile DB に記録する。
* トークン名の送信者が同トークンの所有者で、かつメッセージに含まれる文字列が `delete` だったとき、下記の処理を行う。
 * アドレスとハッシュの紐付けを削除する。
 * メタデータの記録を削除する。

Scanner は、Monacoin チェーンの 1 版から順に読み volatile DB の更新を行う。
そのため、Owner や Register が同じトークンに対して送信した場合には、あとから出した送信により上書きされる。

Scanner が volatile DB を更新した後に、Owner が他の人にトークンの所有権を transfer した場合、Scanner はトークンの所有権が引き続き Owner にあるものと見做す。
アドレスとハッシュの所有権を変更するには、新しいトークン所有権者が、Owner となって(または Register を介して) update 更新作業を行う必要がある。

# Library Reference

Library Reference (以下 Reference と略記)は、Audience からの「特定トークンに紐付いた、ハッシュおよびメタデータの内容」に関する問い合わせに対し応答する。

