﻿# Autoprefixer Rails [![Build Status](https://travis-ci.org/ai/autoprefixer-rails.svg)](https://travis-ci.org/ai/autoprefixer-rails)

<img align="right" width="94" height="71" src="http://postcss.github.io/autoprefixer/logo.svg" title="Autoprefixer logo by Anton Lovchikov">

[Autoprefixer] is a tool to parse CSS and add vendor prefixes to CSS rules
using values from the [Can I Use]. This gem provides Ruby and Ruby on Rails
integration with this JavaScript tool.

Windows users should install [node.js](http://nodejs.org/).
Autoprefixer Rails doesn’t work with old JScript in Windows.

<a href="https://evilmartians.com/?utm_source=autoprefixer-rails">
<img src="https://evilmartians.com/badges/sponsored-by-evil-martians.svg" alt="Sponsored by Evil Martians" width="236" height="54">
</a>

[Autoprefixer]:  https://github.com/postcss/autoprefixer
[Can I Use]:     http://caniuse.com/

## Usage

### Ruby on Rails

Add the `autoprefixer-rails` gem to your `Gemfile`:

```ruby
gem "autoprefixer-rails"
```

Clear your cache:

```sh
rake tmp:clear
```

Write your CSS (Sass, Stylus, LESS) rules without vendor prefixes
and Autoprefixer will apply prefixes for you.
For example in `app/assets/stylesheet/foobar.sass`:

```sass
:fullscreen a
  display: flex
```

Autoprefixer uses Can I Use database with browser statistics and properties
support to add vendor prefixes automatically using the Asset Pipeline:

```css
:-webkit-full-screen a {
    display: -webkit-box;
    display: -webkit-flex;
    display: flex
}
:-moz-full-screen a {
    display: flex
}
:-ms-fullscreen a {
    display: -ms-flexbox;
    display: flex
}
:fullscreen a {
    display: -webkit-box;
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex
}
```

If you need to specify browsers for your Rails project, you can save them
to `browserslist` config in app root. See [Browserslist docs] for config format.

```
last 1 version
> 1%
Explorer 10
```

You can get what properties will be changed using a Rake task:

```sh
rake autoprefixer:info
```

[Browserslist docs]: https://github.com/ai/browserslist
[Firefox ESR]:       http://www.mozilla.org/en/firefox/organizations/faq/

### Sprockets

If you use Sinatra or other non-Rails frameworks with Sprockets,
just connect your Sprockets environment with Autoprefixer and write CSS
in the usual way:

```ruby
assets = Sprockets::Environment.new do |env|
  # Your assets settings
end

require "autoprefixer-rails"
AutoprefixerRails.install(assets)
```

### Ruby

If you need to call Autoprefixer from plain Ruby code, it’s very easy:

```ruby
require "autoprefixer-rails"
prefixed = AutoprefixerRails.process(css, from: 'main.css').css
```

You can specify browsers by `browsers` option:

```ruby
AutoprefixerRails.process(css, from: 'a.css', browsers: ['> 1%', 'ie 10']).css
```

## Visual Cascade

By default, Autoprefixer will change CSS indentation to create nice visual
cascade of prefixes.

```css
a {
  -webkit-box-sizing: border-box;
     -moz-box-sizing: border-box;
          box-sizing: border-box
}
```

You can disable it by `cascade: false` in `config/autoprefixer.yml`
or in `process()` options.

## Safe Mode

If you have legacy code with non-valid CSS hacks, you can enable Safe Mode
and Autoprefixer will try to fix broken CSS.

Add `safe: on` to `config/autoprefixer.yml` or use `safe: true` option
in `process` method.

## Source Map

Autoprefixer will generate source map, if you set `map` option to `true` in
`process` method.

You must set input and output CSS files paths (by `from` and `to` options)
to generate correct map.

```ruby
result = AutoprefixerRails.process(css,
    map:   true,
    from: 'main.css',
    to:   'main.out.css')
```

Autoprefixer can also modify previous source map (for example, from Sass
compilation). Just set original source map content (as string) to `map` option:

```ruby
result = AutoprefixerRails.process(css, {
    map:   File.read('main.sass.css.map'),
    from: 'main.sass.css',
    to:   'main.min.css')

result.map #=> Source map from main.sass to main.min.css
```

See all options in [PostCSS docs]. AutoprefixerRails will convert Ruby style
to JS style, so you can use `map: { sources_content: false }`
instead of camelcase `sourcesContent`.

[PostCSS docs]: https://github.com/postcss/postcss#source-map-1
