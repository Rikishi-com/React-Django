<file name=0 path=/Users/yuki/Documents/GitHub/React-Django/django.md># コマンド一覧

```bash
django-admin startproject drfapi .
```

```bash
django-admin startapp api
```

**INSTALLED APPS**の基本的な使い方
- Djangoのアプリを登録する

**INSTALLED APPS**の基本的な意味
- マイグレーション対象になる
- 管理画面で使用できる
- テンプレートやシグナルに登録される
- Djangoに読み込まれるようになる

**マイグレーション**とは
- Pythonのコードで作ったモデル（=データベースの設計）を，実際のデータベースのテーブルに変換・更新すること

##　プロジェクト直下に自動生成される主なファイルとその役割

|ファイル名|説明|例|
|---|---|---|
|settings.py|プロジェクト全体の設定ファイル|使用するアプリ（INSTALLED_APPS），データベース設定（DATABASES），静的ファイルの場所（STATIC_URL）など様々|
|urls.py|URLルーティングの設定ファイル|どのURLにアクセスしたとき，どの処理（view関数）を呼び出すかを定義|
|wsgi.py|本番環境でDjangoを動かすためのエントリポイント|変更する必要は殆どない|
|asgi.py|ASGIサーバ用|WebSocketやasync対応する際に使う|

---

##　アプリ内に生成される主なファイルとその役割

|ファイル名|説明|例|
|---|---|---|
|views.py|ユーザーからのリクエストに対してレスポンスを返す処理を書く|HTML表示・APIレスポンスなどのロジック|
|models.py|データベースのテーブル構造（モデル）を定義する|クラスベースでDBのテーブルを定義する|
|admin.py|管理画面で使うモデルの表示・設定を記述する|モデルを `admin.site.register()` で登録|
|apps.py|このアプリの設定ファイル|`INSTALLED_APPS` で読み込むときに使われる|
|tests.py|自動テストコードを記述するファイル|ユニットテストや動作確認に使う|
|migrations/|マイグレーションファイルが保存されるフォルダ|`makemigrations` で自動生成される|

---

## `models.py`の構文例

```python
class Task(models.Model):   # models.Modelは引数ではなく，継承するモデル
    title = models.CharField(max_length=200)
    description = models.TextField(blank=True, null=True)
    completed = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):  #selfは自身を指す引数
        return self.title
```
- `class`：設計図のようなもの
- `models.Model`：Djangoが用意している基本のモデルクラス．これを継承させることで，TaskクラスがDjangoモデル（=データベースの１テーブルの定義）として認識される
- 継承：親クラスから機能（マイグレーション，ORM，フィールド定義など）を継承していて，それに加えて子クラス独自の物を付ける

### `__str__` メソッドの役割
- `__str__()` は、オブジェクトを文字列として表現する際に使われるメソッド（Pythonの特殊メソッド）
- 管理画面やシェルでモデルのインスタンスを表示する際に、`Task object` のような表示ではなく、`self.title` の値を表示できるようにする
- 例：`Task(title="買い物")` というオブジェクトが `print()` や Django admin 上で `"買い物"` として表示されるようになる
- つまり，クラスから作られたインスタンスを一言で表すなら，ということを定義している（今回ならタイトルがクラスから作られたインスタンスを一言で表している）

### `models.Model`の役割
- Djangoに**このクラスはデータベースと連携するモデルである**ということを伝える
- `CharField`などを使えるようになる
- マイグレーションや**ORM(PythonコードでDB操作)**に対応する

---

```bash
python manage.py makemigrations
```

```bash
python manage.py migrate
```

```bash
python manage.py makesuperuser
```

---

## シリアライザー
- Pythonオブジェクト（Taskモデルなど）をJSONなどに変換すること