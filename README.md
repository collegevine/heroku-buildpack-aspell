# heroku-buildpack-aspell

* NOTE: This is an experimental OSS project *

# Installation

This buildpack works best with the multiple buildpacks feature of Heroku.

```
$ heroku buildpacks:add --index 1 https://github.com/xavriley/heroku-buildpack-aspell
$ git commit --allow-empty -m "Add aspell buildpack"
```

# Usage

## `ffi-aspell` gem

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

## `spellchecker` gem

NB this gem is buggy and unmaintained and I wouldn't recommend it, but if you have no option...

```
irb(main):006:0> require 'spellchecker'
irb(main):006:0> Spellchecker.aspell_path = "/app/vendor/aspell/bin/aspell --data-dir='/app/vendor/aspell/lib/aspell-0.60' --dict-dir='/app/vendor/aspell/lib/aspell-0.60'"
=> "/app/vendor/aspell/bin/aspell --data-dir='/app/vendor/aspell/lib/aspell-0.60' --dict-dir='/app/vendor/aspell/lib/aspell-0.60'"
irb(main):007:0> Spellchecker.check "some string"
NameError: uninitialized constant Spellchecker::Tempfile
from /app/vendor/ruby-ruby2.2.4/lib/ruby/gems/2.2.0/gems/spellchecker-0.1.5/lib/spellchecker.rb:19:in `check'
from (irb):7
from /app/bin/irb:15:in `<main>'
irb(main):008:0> require 'tempfile' # buggy!
=> true
irb(main):009:0> Spellchecker.check "some string"
=> [{:original=>"some", :correct=>true}, {:original=>"string", :correct=>true}]
```

All it does is shell out to a tempfile behind the scenes. It also uses class variables which seems like a bad idea.
