# heroku-buildpack-aspell

* NOTE: This is an experimental OSS project *

# Installation

This buildpack works best with the multiple buildpacks feature of Heroku.

```
$ heroku buildpacks:add --index 1 https://github.com/xavriley/heroku-buildpack-aspell
$ git commit --allow-empty -m "Add aspell buildpack"
```

# Usage

Known to work with the `ffi-aspell` gem, with some configuration required.

```
# On the Heroku dyno, assuming a Ruby app with ffi-aspell installed
~ $ irb -r 'ffi/aspell'
irb(main):001:0> speller = FFI::Aspell::Speller.new('en', :'data-dir' => '/app/vendor/aspell/lib/aspell-0.60', :'dict-dir' => "/app/vendor/aspell/lib/aspell-0.60")
=> #<FFI::Aspell::Speller:0x007f50f0654dc8 @config=#<FFI::Pointer address=0x007f50f0a1faa0>, @speller=#<FFI::Pointer address=0x007f50f0a29a80>>
irb(main):002:0> "chcek our terible spelilng".scan(/\w+/).each {|w| "#{w}: did you mean #{speller.suggestions(w).first}?" }
=> ["chcek", "our", "terible", "spelilng"]
irb(main):003:0> "chcek our terible spelilng".scan(/\w+/).each {|w| puts "#{w}: did you mean #{speller.suggestions(w).first}?" }
chcek: did you mean check?
our: did you mean our?
terible: did you mean terrible?
spelilng: did you mean spelling?
=> ["chcek", "our", "terible", "spelilng"]
```
