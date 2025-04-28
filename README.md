# 20250429flask-
20250429flask日本語英語切り替えデモ
# Pythonで実装する！URLパラメータ「lang=en」で簡単に言語切り替えるFlaskアプリケーション

## はじめに

ウェブアプリケーションで多言語対応を実装する方法はいくつかありますが、最もシンプルな方法の一つが「URLパラメータ」を使った方法です。特に「?lang=en」というクエリパラメータを使って英語と日本語を切り替える実装は、小〜中規模のプロジェクトで重宝します。

この記事では、Flaskフレームワークを使って、シンプルな言語切り替え機能を持つデモアプリケーションを作成する方法を解説します。

## 必要な環境

- Python 3.8以上
- Flask

## フォルダ構成

```
lang_demo/                  # プロジェクトのルートディレクトリ
│
├── app.py                  # メインのFlaskアプリケーションファイル
│
└── templates/              # HTMLテンプレートを格納するフォルダ
    └── index.html         # メインページのHTMLテンプレート
```

## 実装コード

### app.py - メインアプリケーション

```python
from flask import Flask, render_template, request

app = Flask(__name__)

# 言語テキスト辞書
texts = {
    'ja': {
        'title': 'こんにちは世界',
        'welcome': 'ようこそ！これは言語切り替えデモです。',
        'description': 'URLに"?lang=en"を追加すると英語に切り替わります。',
        'button': '続ける'
    },
    'en': {
        'title': 'Hello World',
        'welcome': 'Welcome! This is a language switching demo.',
        'description': 'Add "?lang=en" to the URL to switch to English.',
        'button': 'Continue'
    }
}

@app.route('/')
def index():
    # GETパラメータからlang値を取得（デフォルトはja）
    lang = request.args.get('lang', 'ja')
    # 有効な言語コードのみ許可
    lang = lang if lang in ['en', 'ja'] else 'ja'
    
    # 選択された言語のテキストを取得
    current_texts = texts[lang]
    
    # テンプレートに言語とテキストを渡す
    return render_template('index.html', lang=lang, texts=current_texts)

if __name__ == '__main__':
    app.run(debug=True)
```

### templates/index.html - テンプレート

```html
<!DOCTYPE html>
<html lang="{{ lang }}">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ texts.title }}</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
        }
        .container {
            border: 1px solid #ddd;
            padding: 20px;
            border-radius: 5px;
        }
        .lang-switch {
            text-align: right;
            margin-bottom: 20px;
        }
    </style>
</head>
<body>
    <div class="lang-switch">
        {% if lang == 'en' %}
            <a href="/">日本語</a>
        {% else %}
            <a href="/?lang=en">English</a>
        {% endif %}
    </div>
    
    <div class="container">
        <h1>{{ texts.title }}</h1>
        <p>{{ texts.welcome }}</p>
        <p>{{ texts.description }}</p>
        <button>{{ texts.button }}</button>
    </div>
</body>
</html>
```

## 実装の解説

### 1. 言語テキストの管理

```python
texts = {
    'ja': {
        'title': 'こんにちは世界',
        # 日本語のテキスト
    },
    'en': {
        'title': 'Hello World',
        # 英語のテキスト
    }
}
```

言語ごとのテキストを辞書として管理します。各言語コード（'ja'や'en'）をキーとして、その言語で表示するテキストを値として格納しています。

### 2. URLパラメータの取得

```python
# GETパラメータからlang値を取得（デフォルトはja）
lang = request.args.get('lang', 'ja')
# 有効な言語コードのみ許可
lang = lang if lang in ['en', 'ja'] else 'ja'
```

`request.args.get('lang', 'ja')`でURLパラメータから'lang'の値を取得します。値が存在しない場合はデフォルトとして'ja'（日本語）を使用します。

また、不正な値が指定された場合に備えて、有効な言語コード（'en'か'ja'）かどうかをチェックし、無効な場合は'ja'に設定します。

### 3. テンプレートで言語を切り替える

```html
<div class="lang-switch">
    {% if lang == 'en' %}
        <a href="/">日本語</a>
    {% else %}
        <a href="/?lang=en">English</a>
    {% endif %}
</div>
```

現在の言語に応じて、切り替えリンクを表示します。英語表示中は日本語へのリンクを、日本語表示中は英語へのリンクを表示します。

## 起動方法

1. 必要なパッケージをインストール
```bash
pip install flask
```

2. アプリケーションを起動
```bash
python app.py
```

3. ブラウザでアクセス
- 日本語版: http://127.0.0.1:5000/
- 英語版: http://127.0.0.1:5000/?lang=en

## 応用例

### 複数のページでの言語維持

複数のページがある場合、遷移先でも言語設定を維持するには、リンクURLに言語パラメータを追加します：

```html
<a href="{{ url_for('other_page') }}{% if lang == 'en' %}?lang=en{% endif %}">
    {{ texts.other_page_link }}
</a>
```

### 言語の追加

他の言語を追加する場合は、テキスト辞書に言語を追加するだけです：

```python
texts = {
    'ja': { /* 日本語テキスト */ },
    'en': { /* 英語テキスト */ },
    'fr': { /* フランス語テキスト */ },
    'zh': { /* 中国語テキスト */ }
}
```

そして、許可する言語リストも更新します：

```python
lang = lang if lang in ['en', 'ja', 'fr', 'zh'] else 'ja'
```

## まとめ

この実装方法のメリットは以下の通りです：

1. **シンプル**：追加のライブラリやフレームワークが不要
2. **軽量**：国際化フレームワークを使うよりも軽い
3. **わかりやすい**：コードが直感的で理解しやすい
4. **拡張性**：必要に応じて言語を追加できる

一方で、大規模なプロジェクトでは、Flaskのよりパワフルな国際化（i18n）機能や、専用の国際化ライブラリ（Flask-Babelなど）の使用も検討すると良いでしょう。

言語切り替えは、グローバルなウェブアプリケーションにとって重要な機能です。この記事で紹介した手法を活用して、ユーザーフレンドリーな多言語サイトを作成してみてください！

# Flaskアプリケーションのフォルダ構成

シンプルな言語切り替えデモアプリケーションのフォルダ構成は以下のようになります：

```
lang_demo/                  # プロジェクトのルートディレクトリ
│
├── app.py                  # メインのFlaskアプリケーションファイル
│
├── templates/              # HTMLテンプレートを格納するフォルダ
│   └── index.html         # メインページのHTMLテンプレート
│
└── venv/                   # 仮想環境フォルダ (オプション)
```

## 詳細説明

- **lang_demo** - プロジェクトの最上位フォルダです。名前は任意で変更できます。
- **app.py** - アプリケーションのメインファイルで、Flaskの設定とルート定義、言語テキスト管理などが含まれます。
- **templates** - Flaskがテンプレートを探すデフォルトのフォルダです。この名前は変更しないでください。
- **index.html** - メインページのHTMLテンプレートです。
- **venv** - Python仮想環境フォルダ。この名前は変更可能です。

これだけの構成で言語切り替えデモアプリケーションを実行できます。静的ファイル（CSSやJavaScriptなど）を追加する場合は、`static`フォルダを作成します：

```
lang_demo/
│
├── app.py
│
├── templates/
│   └── index.html
│
├── static/                 # 静的ファイル用フォルダ（必要に応じて）
│   └── style.css          # CSSファイル
│
└── venv/
```

from flask import Flask, render_template, request

app = Flask(__name__)

# 言語テキスト辞書
texts = {
    'ja': {
        'title': 'こんにちは世界',
        'welcome': 'ようこそ！これは言語切り替えデモです。',
        'description': 'URLに"?lang=en"を追加すると英語に切り替わります。',
        'button': '続ける'
    },
    'en': {
        'title': 'Hello World',
        'welcome': 'Welcome! This is a language switching demo.',
        'description': 'Add "?lang=en" to the URL to switch to English.',
        'button': 'Continue'
    }
}

@app.route('/')
def index():
    # GETパラメータからlang値を取得（デフォルトはja）
    lang = request.args.get('lang', 'ja')
    # 有効な言語コードのみ許可
    lang = lang if lang in ['en', 'ja'] else 'ja'
    
    # 選択された言語のテキストを取得
    current_texts = texts[lang]
    
    # テンプレートに言語とテキストを渡す
    return render_template('index.html', lang=lang, texts=current_texts)

if __name__ == '__main__':
    app.run(debug=True)
