---
layout: post
title:  "The Cashier: A Stripe Primer"
date:   2015-08-27 12:00:00
categories: payments stripe
---

Welcome to my first blog post! Sorry about the delay between my welcome and initial technical post -- I've been very busy so far this summer. This blog post will mainly be focusing on payments, and how to interact with Stripe's Ruby library.

So, let's get started by talking about what Stripe actually is. Stripe
is a company which provides a simple API (and client libraries in many
different programming languages) which allows developers to process
payments, and to charge end users. Due to the great documentation and
simplicity of their API, Stripe has become very popular across the web.

Let's dive into how you would go about creating subscriptions for users
(known as customers in Stripeland).

## Prerequisite Steps
- Signup for a Stripe account
- Create a Plan model that has the following data attributes:
`name:string usd_monthly_cost:string`

## Step 1: Create a Stripe config initializer
- Create `config/initializers/stripe.rb`, and add the following:

{% highlight ruby %}

require "stripe"
Stripe.api_key = ENV["STRIPE_API_KEY"]

{% endhighlight %}

## Step 2: Create plans
- Create a Rake task that will bootstrap your plans:

{% highlight ruby %}

require "stripe"

namespace :stripe do
  task :create_plans => [:environment] do
    Stripe.api_key = ENV["STRIPE_API_KEY"]
    Plan.all.each do |plan|
      cost_to_cents = plan.usd_monthly_cost * 100
      Stripe::Plan.create(
        :amount => cost_to_cents,
        :interval => "month",
        :name => plan.name,
        :currency => "usd",
        :id => plan.id
      )
      puts "Created Stripe plan for #{plan.name}"
    end
  end
end

{% endhighlight %}

## Step 3: Setup Stripe's JS library

It may seem a little weird that you have to include Stripe's JS library,
but it's very important to have on the front end because it generates
the token you need to use to be able to charge credit cards. No Stripe
JS means no card charges!

- Include the following your application.html.erb file:

{% highlight erb %}

<%= javascript_include_tag "https://js.stripe.com/v1/" %>
<meta name="stripe-key" content="<%= ENV["STRIPE_PUBLISHABLE_KEY"] %>" />

{% endhighlight %}

- Create a `payments.coffee` file (or whatever you'd like to name it), and add the
following. Don't forget to change the CSS selectors, and CoffeeScript
function names to fit your form and use case!

{% highlight coffeescript %}

jQuery ->
  Stripe.setPublishableKey($('meta[name="stripe-key"]').attr('content'))
  user.setupForm()

user =
  setupForm: ->
    $('#new_user').submit ->
      $('input[type=submit]').attr('disabled', true)
      if $('#card_number').length
        user.processCard()
        false
      else
        true

  processCard: ->
    card =
      number: $('#card_number').val()
      cvc: $('#card_code').val()
      expMonth: $('#card_month').val()
      expYear: $('#card_year').val()
    console.log "prep for stripe createToken"
    Stripe.createToken(card, user.handleStripeResponse)

  handleStripeResponse: (status, response) ->
    if status == 200
      console.log "200"
      $('#stripe_card_token').val(response.id)
      $('#new_user')[0].submit()
    else
      $('#stripe_error').text(response.error.message)
      $('input[type=submit]').attr('disabled', false)

{% endhighlight %}

## Step 4: Create Stripe API objects

- Where you have users signing up for subscriptions, add the following.
You may want to store the `stripe_customer.id` and
`stripe_subscription.id` values in the database, so you can edit
subscriptions in the future. An example of when you would need this, is
if a user wants to change their subscription's plan.

{% highlight ruby %}

# Change this line to fit how the plan_id is being sent in.
plan_id = Plan.find(params[:plan_id]).id

stripe_customer = Stripe::Customer.create(
  source: card_token,
  email: user.email
)
stripe_subscription = stripe_customer.subscriptions.create(:plan => plan_id)

{% endhighlight %}

## Tada!

Stripe may seem a little complicated to setup at first, but when you
look at their [non-Ruby API docs](http://stripe.com/docs), and their [Ruby specific docs](http://stripe.com/docs/api/ruby) and browse around StackOverflow, any question
you have will likely be answered.

## Ways to Improve

- Want to be really fancy? Move all of your Stripe API calls to a service
object (a.k.a. just a Ruby class - things always have special names in Railsland). I named my Stripe payments service object `Cashier`. This can make your Stripe code reusable across
your app, and can also organize all of your Stripe code in one location.
- Write tests! Tests are a super valuable way to verify that your code
actually works the way you think it does. I always try to write tests
after I write any Ruby code.

## Wrap Up

Ping me on [Twitter](https://twitter.com/applerebel)
or [open up an issue on the blog's GitHub
repo](https://github.com/maclover7/sparklingcode.co/issues/new) if you
have any questions.

# That's all, folks!
