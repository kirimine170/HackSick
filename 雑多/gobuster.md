<!-- ---
title: "{なんでもいいんだよぉ！}"
type: "雑多:Others"
tags: "雑多 Others Other template"
--- -->

# gobuster

gobusterはWebサイトのURI(ディレクトリやファイル)や、DNSのサブドメインなどの列挙(ブルートフォース攻撃)を行うためのツールです。

## 使い方の説明

構文: `gobuster [command]`

gobusterコマンドは後に続くcommandで何を指定するかによってモードが切り替わるので、今回はその中で`vhost`と`dir`モードの時の基本的な操作について説明します。主にHTBのStarting Pointで用いた駒野を例に解説し、詳細な説明やその他のモード(`dns`,`s3`,`fuzz`,etc.)の使い方については追記求みます。//FIXME

### gobuster vhost

commandとしてvhostというキーワードを指定しvhostモードを起動すると、仮想ホストの列挙を行うための操作を行うことができます。具体的にどのようなことをするのかは`gobuster vhost [flags]`の形式でフラグを与えて指定します。AWSによって動いているサーバなどに対する

e.g. `gobuster vhost -w ./wordlists/subdomains-top1million-5000.txt -u http://foobar.com --append-domain`

上記コマンドはHTBのチュートリアルで用いたコマンドを改変したものです。これを例にして説明します。  

フラグについての説明:  

- **-w** は使用するワードリストを指定します。ここで指定されたファイル内の単語を用いてブルートフォース攻撃によるvhostの列挙が行われます。つまり、このファイルに含まれている単語と合致するvhostが発見できる可能性があります。  
使用しているsubdomains-top1million-5000.txtは、よく用いられるサブドメインの名称一覧で、[subdomains-top1million-5000](https://github.com/danielmiessler/SecLists/blob/master/Discovery/DNS/subdomains-top1million-5000.txt)
- **-u** は対象URLの指定を行うフラグです。
- **--append-domain** はURLのメインドメイン(上の例では`foobar.com`)をワードリストの単語に追加します。例えばワードリストから`www`という単語が取り出されたときには、`www.foobar.com`というドメインに対して探索が行われます。
サブドメインの列挙に便利なフラグです。

これにより、`http://foobar.com`のサブドメインが列挙でき、例えば`http://s3.foobar.com`などが見つかる可能性があります。

### gobuster dir

dirモードではディレクトリやファイルの列挙(ディレクトリトラバーサル攻撃)を行うことができます。対象のサーバにアクセスし、指定したファイルが存在するるかどうかを確認できる状況になった場合に次のようなコマンドが使用できる場合がある。  

e.g. `gobuster dir -u {TARGET-IP} -w /wordlists/dirbuster/directory-list-2.3-small.txt -x php`

上の例では対象IPに対してワードリストに存在するファイルが存在するかどうかを調査できます。  

フラグについての説明:

- **-u** 対象の指定はURL以外にもIPアドレスによる指定でも可能です。
- **-w** ワードリストの指定。今回はdirbusterのワードリストを使用しました。[dirbuster-wordlist](https://github.com/daviddias/node-dirbuster/blob/master/lists/directory-list-2.3-medium.txt)  
- **-x** 探索対象の拡張子を指定することができる。今回は.phpのファイルを探す目的で使用していたためこのフラグを指定したが、なくても動作はする
