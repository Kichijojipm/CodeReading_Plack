# Plackコードリーディング　レジュメ

- 日時：2015/2/25 19:30~21:30
- 場所：コワーキングスペース「ぴこす」

## Plackとは?

- PSGI

	PerlにおけるWebサーバと、Webアプリケーションのインタフェース規約
	
	[`http://search.cpan.org/perldoc?PSGI`](http://search.cpan.org/perldoc?PSGI)
	
- Plack

	PSGIの実装
	
	[`http://search.cpan.org/perldoc?Plack`](http://search.cpan.org/perldoc?Plack)

## メリット

- WebサーバとWAFの組み合わせが自由に選べる
- 多数のmiddlewareが提供されていて、WebサーバやWAFに依存しない拡張ができる

## Plackのソースコードの入手

    $ git clone git@github.com:plack/Plack.git

## Plackのインストール

	$ cpanm Plack

## 基本的なインタフェース

HTTPのステータスと、ヘッダー、ボディを配列に入れて渡す。

	# Hello.psgi
	my $app = sub {
	  my $env = shift;
	  # ...
	  return [ $status, $headers, $body ];
	};


## とりあえず動かしてみる

psgiファイルを用意する。

`SampleApp.psgi`

	my $handler = sub {
	    return [ 200, [ "Content-Type" => "text/plain", "Content-Length" => 11 ], [ "Hello World" ] ];
	};

起動してみます。

    $ plackup --host localhost --port 9090 SampleApp.psgi
    
ブラウザから`http://localhost:9090`へアクセスしてみると、「Hello World」と表示されます。

次に、Plack Middlewareを追加してみます。

`SampleAppMD5.psgi`

	use Plack::Builder;

	my $handler = sub {
	    return [ 200, [ "Content-Type" => "text/plain", "Content-Length" => 11 ], [ "Hello World" ] ];
	};

	builder {
	    enable "Plack::Middleware::ContentMD5";
	    $handler;
	};

起動してみます。

    $ plackup --host localhost --port 9090 SampleAppMD5.psgi

ヘッダにMD5が追加されていることが分かると思います。

[この図](http://docs.pylonsproject.org/projects/pylons-webframework/en/latest/concepts.html#wsgi-middleware)を見てみると、構造がよく分かると思います。

## 主要なモジュール

今日は、上記で出てきた、下記のモジュールを見ながら、動作を追いかけてみます。

    Plack::Handler
    Plack::Loader
    Plack::Builder

