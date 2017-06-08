# Devise Confirmable + Mailgun

우선 `Devise` gem 설치

```
$ rails generate devise:install
```

```ruby
# config/development.rb
config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
```

```
$ rails generate devise User
```

```
$ rails g migration AddConfirmableToDevise
```

``` ruby
# add_confirmable_to_devise
t.string   :confirmation_token
t.datetime :confirmed_at
t.datetime :confirmation_sent_at
t.string   :unconfirmed_email # Only if using reconfirmable
```

```
$ rails db:migrate
```

Devise는 confirmed_at이 nil이면 인증되지않은 유저이다. 그리고 이메일 인증을 완료했을 때 confirmed_at컬럼에 인증 완료된 시간 값이 입력되면서 인증이 된다.

```ruby
# user.rb
class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable, :confirmable
end
```

### Mailer

레일즈에서는 `Mailer`를 이용해서 메일을 보낸다. `Devise`도 `Mailer`를 제공한다.
[Mailer guide](http://guides.rubyonrails.org/action_mailer_basics.html)
[Mailer guide - 한글](http://guides.rorlab.org/action_mailer_basics.html)


#### SMTP(Simple Mail Transfer Protocol)

사용자들은 SMTP를 메일을 보내는데 주로 사용한다. 이때 [Mailgun](https://www.mailgun.com/)의 도움을 받아서 설정할 것이다.

- 회원가입할 때 카드등록은 안해도된다.

```ruby
# development.rb
Rails.application.configure do

	...

  config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
  config.action_mailer.delivery_method = :smtp
  config.action_mailer.smtp_settings = {
    port: 587,
    address: "smtp.mailgun.org",
    user_name: "your_user_name",
    password: "your_password",
    domain: "your_domain",
    authentication: :plain,
    enable_starttls_auto: true
  }

end
```

이때 보안을 위해서는 Figaro gem을 이용한다.