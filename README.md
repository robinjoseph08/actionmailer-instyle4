# ActionMailer::InStyle

Forked From [testpilot/actionmailer-instyle](https://github.com/testpilot/actionmailer-instyle). He did most of the hard work.

HTML Emails can be a PITN, especially when you want maximum email client compatibility, of which the best way to achieve this is inline CSS. Unfortunately nobody actually wants to be forced into writing crap like that so we've created **InStyle**, it will automatically intercept your emails and look for a `stylesheet` linking to something inside your assets folder, extract all the styles, and convert them to inline styles on the fly.

_There are a couple of other projects which do this, some have not been updated for a while and none of them work with the Rails 3.1 asset pipeline._ *InStyle* uses Sprockets to render CSS which means you can use Sass, Compass, and any other CSS hackery you desire, including using stylesheets you would usually only use within your views e.g If you want to email an activity digest and make it look the same as what the user is used to seeing within your application (think Yammer).

## Requirements

This gem is built to take advantage of the Rails 3.1 *(and above)* Asset Pipeline, as such you must be using Rails ~>3.1 and have the Asset Pipeline enabled (default)

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'actionmailer-instyle4', :require => 'action_mailer/in_style'
```

And then execute:

```bash
$ bundle install
```

## Usage

Once installed it will add the required hooks out of the box to intercept each email being sent.

**Stylesheets**

As with all your other stylesheets, put them in `app/assets/stylesheets` and include them into your mailer
template using `stylesheet_link_tag "account_mailer"`, where `account_mailer` is the filename less the extension of the stylesheet you want to use.

Everything runs through the Asset Pipeline so you can use Sass simply by adding the `.scss` extension as you would with all other stylesheets.

**Compass**

Because Sass works out of the box, you can also include Compass for browser specific mixins and useful CSS3 helpers, keeping in mind that some email clients (Outlook for example) use very dated rendering engines and a lot of CSS properties may have undesired effects or not work at all.

For a complete rundown on what is supported, checkout this guide which CampaignMonitor has compiled.

[CampaignMonitor's guide to CSS support in email](http://www.campaignmonitor.com/css/)

## Tutorial

**1. Generate a Mailer:**

    rails g mailer account_mailer account_created_email

**2. Create a template to be used for emails this Mailer sends:**

In `app/views/layouts/account_mailer.html.haml`

```haml
%html
  %head
    = stylesheet_link_tag "account_mailer"

  %body
    %table.main
      %tr
        %td= yield
```

You will notice that we have added a stylesheet link in the `head`, this location is not crucial, but the use of `stylesheet_link_tag` is because it will generate a link which we can process and feed through Sprockets in order to generate the inline styles.

**3. The stylesheet.**

You can use any style off CSS you like, we're using Sass.

`app/assets/stylesheets/account_mailer.css.scss`:

```css
body {
  font-family: 'Helvetica Neue', Helvetica, sans-serif;
  -webkit-font-smoothing: antialiased;
  width: 100%;
  background: darken(white, 8%);
}

table {
  background: white;
  &.main {
    width: 430px;
    tr td {
      padding: 5px;
    }
  }
}
```

**4. Email views.**

In order to take advantage of all this fancy work, you must define a html view for your email, and optionally a text version. By default we will automatically generate a text version from the text content of the html view if none is supplied.

`app/views/account_mailer/account_created_email.html.haml`:

```haml
%p Hi #{@user.first_name},
%p Your account has been successfully created, so don't waste any time, #{link_to "Get started now", get_started_url}
```

**5. Send**

```ruby
AccountMailer.account_created_email(@user).deliver
```

**6. Send awesome emails.**

The resulting email will look something like this:

```html
<html>
<head><meta http-equiv="Content-Type" content="text/html; charset=UTF-8"></head>
<body style="font-family: 'Helvetica Neue', Helvetica, sans-serif; -webkit-font-smoothing: antialiased; width: 100%; background-color: #ebebeb;" bgcolor="#ebebeb">
  <table style="width: 430px; background-color: white;" bgcolor="white">
    <tr>
      <td style="padding: 5px;">
        <p>Hi Michael,</p>
        <p>Your account has been successfully created, so don't waste any time, <a href="/started">Get started now</a></p>
      </td>
    </tr>
  </table>
</body>
</html>
```

**7. Grab a beer.**

As above.

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Added some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request

## Meta

| Author | Twitter |
|------:|:------------|
| Robin Joseph | [@robinjoseph08](http://twitter.com/robinjoseph08) |
