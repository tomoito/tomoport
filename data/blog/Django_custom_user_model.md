---
title: 'DjangoのUserモデルの概要'
date: '2021-05-10'
tags: ['python', 'django']
draft: false
summary: 'DjangoのカスタムUserモデルの作成方法を簡単に説明します。'
---

# Django の User モデルの概要について

Django にはビルトインの User モデルが存在し、その User モデルに合わせた認証システムが組み込まれています。

User モデルの主要な属性は次のとおりです。

1. username
2. password
3. email
4. first_name
5. last_name

その他の項目は以下を参照してください。
https://docs.djangoproject.com/ja/3.2/ref/contrib/auth/#django.contrib.auth.models.User

User モデルの属性を変更したい、または、認証のモデルを変更（例えばユーザー名の代わりにメールアドレスに変更）したい場合は「既存のモデルを拡張する」か「カスタム User を利用する」必要があります。

しかしながら、公式ドキュメントでは以下のように言っています。

「新しくプロジェクトを始める場合は、デフォルトの User で十分である場合でも、カスタムユーザーモデルを作成することを強く推奨します。」
https://docs.djangoproject.com/ja/3.1/topics/auth/customizing/#using-a-custom-user-model-when-starting-a-project

というわけなので、全てのケースでカスタム User を使いましょう。

カスタ User を拡張するには、２つの方法があります。

・「AbstractUser を継承する方法」

・「AbstractBaseUser を継承する方法

■「AbstractUser を継承する方法」

AbstractUser はデフォルトの User モデルが持っている機能を全て持っており、デフォルト User に少し手を入れたい場合に向いています。

■AbstractBaseUser を継承する方法

AbstructBaseUser を継承したモデルは、認証機能しか含まれておらず、実際のフィールド情報は自分で定義する必要があります。ゆえにカスタマイズ性が高く融通がききます。

AbstractBaseUser はカスタマイズ性が高い判明、自分で全てのフィールドを定義する必要がある。

## **カスタムユーザーモデルをデフォルトに設定**

自分で作った User モデルをデフォルトで使用するようにせんげんします。

`settings.py`に、以下を付け加えましょう。

## Settings.py

```python
`AUTH_USER_MODEL = 'core.CustomUser'`
```

UserManager を作成します

カスタムユーザーモデルを作るためには 2 つのクラス(BaseUserManager, AbstractBaseUser)を実装する必要があります。

core/models.py

```python

from django.db import models

from django.contrib.auth.models import AbstractBaseUser,BaseUserManager,PermissionsMixin

class UserManager(BaseUserManager):

    def create_user(self, email, password=None, **extra_fields):
        if not email:

        raise ValueError('email is must')
        user = self.model(email=self.normalize_email(email), **extra_fields)
        user.set_password(password)
        user.save(using=self._db)

    def create_superuser(self, email, password):
        user = self.create_user(email, password)
        user.is_staff = True
        user.is_superuser = True
        user.save(using= self._db)

        return user

class User(AbstractBaseUser,PermissionsMixin):
    email = models.EmailField(max_length=50, unique=True)
    is_active = models.BooleanField(default=True)
    is_staff = models.BooleanField(default=False)
    objects = UserManager()
    USERNAME_FIELD = 'email'

    def __str__(self):
       return self.email
```

管理画面用に設定する必要もありますが、今回は User モデルにフォーカルしてますので割愛します。
