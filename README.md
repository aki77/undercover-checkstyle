It's now recommended to use [reviewdog-action-code-coverage](https://github.com/aki77/reviewdog-action-code-coverage) instead of this approach.

----

# undercover-checkstyle

![Actions Demo](https://i.gyazo.com/881890dcba76cf30a8db86d9635ba38a.png)

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'undercover-checkstyle'
```

And then execute:

    $ bundle install

## Usage

[Setting up required LCOV reporting](https://github.com/grodowski/undercover#setting-up-required-lcov-reporting)

```
bundle exec undercover-checkstyle
```

### GitHub Actions Example

*reviewdog.yml*
```yaml
runner:
  undercover:
    cmd: bundle exec undercover-checkstyle --compare origin/master
    level: warning
    format: checkstyle
```

*.github/workflows/ruby.yml*
```yaml
name: Ruby

on:
  pull_request:

jobs:
  rspec:
    runs-on: ubuntu-latest

    env:
      RUBYOPT: -EUTF-8

    steps:
      - uses: actions/checkout@v3

      - name: Set up Ruby
        uses: ruby/setup-ruby@v1
        with:
          bundler-cache: true

      - name: Rspec
        run: bundle exec rspec

      - uses: reviewdog/action-setup@v1
        with:
          reviewdog_version: latest

      # NOTE: Uncommenting removes every past `undercover` comment
      # - uses: aki77/delete-pr-comments-action@v1
      #   with:
      #     token: ${{ secrets.GITHUB_TOKEN }}
      #     bodyContains: "[undercover]"
      #     noReply: "true"

      - name: Run reviewdog
        env:
          REVIEWDOG_GITHUB_API_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          git fetch --no-tags
          reviewdog -reporter=github-pr-review -runners=undercover
```

## Thanks

- [grodowski/undercover: Actionable code coverage \- detects untested code blocks in recent changes](https://github.com/grodowski/undercover)
