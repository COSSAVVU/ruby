# NEWS for Ruby 3.4.0

This document is a list of user-visible feature changes
since the **3.3.0** release, except for bug fixes.

Note that each entry is kept to a minimum, see links for details.

## Language changes

* String literals in files without a `frozen_string_literal` comment now emit a deprecation warning
  when they are mutated.
  These warnings can be enabled with `-W:deprecated` or by setting `Warning[:deprecated] = true`.
  To disable this change, you can run Ruby with the `--disable-frozen-string-literal`
  command line argument. [[Feature #20205]]

* `it` is added to reference a block parameter. [[Feature #18980]]

* Keyword splatting `nil` when calling methods is now supported.
  `**nil` is treated similarly to `**{}`, passing no keywords,
  and not calling any conversion methods.  [[Bug #20064]]

* Block passing is no longer allowed in index assignment
  (e.g. `a[0, &b] = 1`).  [[Bug #19918]]

* Keyword arguments are no longer allowed in index assignment
  (e.g. `a[0, kw: 1] = 2`).  [[Bug #20218]]

* GC.config added to allow setting configuration variables on the Garbage
  Collector. [[Feature #20443]]

* GC configuration parameter `rgengc_allow_full_mark` introduced. When `false`
  GC will only mark young objects. Default is `true`. [[Feature #20443]]

## Core classes updates

Note: We're only listing outstanding class updates.

* Exception

    * Exception#set_backtrace now accepts arrays of Thread::Backtrace::Location.
      Kernel#raise, Thread#raise and Fiber#raise also accept this new format. [[Feature #13557]]

* Range

    * Range#size now raises TypeError if the range is not iterable. [[Misc #18984]]
    * Range#step now consistently has a semantics of iterating by using `+` operator
      for all types, not only numerics. [[Feature #18368]]

        ```ruby
        (Time.utc(2022, 2, 24)..).step(24*60*60).take(3)
        #=> [2022-02-24 00:00:00 UTC, 2022-02-25 00:00:00 UTC, 2022-02-26 00:00:00 UTC]
        ```

* RubyVM::AbstractSyntaxTree

    * Add RubyVM::AbstractSyntaxTree::Node#locations method which returns location objects
      associated with the AST node. [[Feature #20624]]
    * Add RubyVM::AbstractSyntaxTree::Location class which holds location information. [[Feature #20624]]

* Fiber::Scheduler

    * An optional `Fiber::Scheduler#blocking_region` hook allows blocking operations to be moved out of the event loop
      in order to reduce latency and improve multi-core processor utilization. [[Feature #20855]]

## Stdlib updates

* Tempfile

    * The keyword argument `anonymous: true` is implemented for Tempfile.create.
      `Tempfile.create(anonymous: true)` removes the created temporary file immediately.
      So applications don't need to remove the file.
      [[Feature #20497]]

* win32/sspi.rb

    * This library is now extracted from the Ruby repository to [ruby/net-http-sspi].
      [[Feature #20775]]

The following default gem is added.

* win32-registry 0.0.1

The following default gems are updated.

* RubyGems 3.6.0.dev
* bundler 2.6.0.dev
* date 3.4.0
* erb 4.0.4
* etc 1.4.4
* fiddle 1.1.5.dev
* fileutils 1.7.3
* io-console 0.7.2
* ipaddr 1.2.7
* irb 1.14.1
* json 2.8.0.alpha1
* logger 1.6.1
* net-http 0.4.1
* optparse 0.5.0
* prism 1.0.0
* psych 5.2.0.beta1
* rdoc 6.7.0
* reline 0.5.10
* resolv 0.4.0
* stringio 3.1.2.dev
* strscan 3.1.1.dev
* syntax_suggest 2.0.1
* time 0.4.0
* uri 0.13.1
* win32ole 1.9.0
* zlib 3.1.1

The following bundled gem is added.

* repl_type_completor 0.1.7

The following bundled gems are updated.

* minitest 5.25.1
* power_assert 2.0.4
* rake 13.2.1
* test-unit 3.6.2
* rexml 3.3.9
* rss 0.3.1
* net-ftp 0.3.8
* net-imap 0.5.0
* net-smtp 0.5.0
* rbs 3.6.1
* typeprof 0.21.11
* debug 1.9.2
* racc 1.8.1

The following bundled gems are promoted from default gems.

* mutex_m 0.2.0
* getoptlong 0.2.1
* base64 0.2.0
* bigdecimal 3.1.8
* observer 0.1.2
* abbrev 0.1.2
* resolv-replace 0.1.1
* rinda 0.2.0
* drb 2.2.1
* nkf 0.2.0
* syslog 0.1.2
* csv 3.3.0

See GitHub releases like [GitHub Releases of Logger] or changelog for
details of the default gems or bundled gems.

[ruby/net-http-sspi]: https://github.com/ruby/net-http-sspi
[GitHub Releases of Logger]: https://github.com/ruby/logger/releases

## Supported platforms

## Compatibility issues

* Error messages and backtrace displays have been changed.

    * Use a single quote instead of a backtick as an opening quote. [[Feature #16495]]
    * Display a class name before a method name (only when the class has a permanent name). [[Feature #19117]]
    * Kernel#caller, Thread::Backtrace::Location’s methods, etc. are also changed accordingly.

        Old:
        ```
        test.rb:1:in `foo': undefined method `time' for an instance of Integer
                from test.rb:2:in `<main>'
        ```

        New:
        ```
        test.rb:1:in 'Object#foo': undefined method 'time' for an instance of Integer
                from test.rb:2:in '<main>'
        ```

* Hash#inspect rendering have been changed. [[Bug #20433]]

    * Symbol keys are displayed using the modern symbol key syntax: `"{user: 1}"`
    * Other keys now have spaces around `=>`: `'{"user" => 1}'`, while previously they didn't: `'{"user"=>1}'`

## Stdlib compatibility issues

## C API updates

* `rb_newobj` and `rb_newobj_of` (and corresponding macros `RB_NEWOBJ`, `RB_NEWOBJ_OF`, `NEWOBJ`, `NEWOBJ_OF`) have been removed. [[Feature #20265]]
* Removed deprecated function `rb_gc_force_recycle`. [[Feature #18290]]

## Implementation improvements

* Array#each is rewritten in Ruby for better performance [[Feature #20182]].

## JIT

## Miscellaneous changes

* Passing a block to a method which doesn't use the passed block will show
  a warning on verbose mode (`-w`).
  [[Feature #15554]]

* Redefining some core methods that are specially optimized by the interpreter
  and JIT like String#freeze or Integer#+ now emits a performance class
  warning (`-W:performance` or `Warning[:performance] = true`).
  [[Feature #20429]]

[Feature #13557]: https://bugs.ruby-lang.org/issues/13557
[Feature #15554]: https://bugs.ruby-lang.org/issues/15554
[Feature #16495]: https://bugs.ruby-lang.org/issues/16495
[Feature #18290]: https://bugs.ruby-lang.org/issues/18290
[Feature #18368]: https://bugs.ruby-lang.org/issues/18368
[Feature #18980]: https://bugs.ruby-lang.org/issues/18980
[Misc #18984]:    https://bugs.ruby-lang.org/issues/18984
[Feature #19117]: https://bugs.ruby-lang.org/issues/19117
[Bug #19918]:     https://bugs.ruby-lang.org/issues/19918
[Bug #20064]:     https://bugs.ruby-lang.org/issues/20064
[Feature #20182]: https://bugs.ruby-lang.org/issues/20182
[Feature #20205]: https://bugs.ruby-lang.org/issues/20205
[Bug #20218]:     https://bugs.ruby-lang.org/issues/20218
[Feature #20265]: https://bugs.ruby-lang.org/issues/20265
[Feature #20429]: https://bugs.ruby-lang.org/issues/20429
[Bug #20433]:     https://bugs.ruby-lang.org/issues/20433
[Feature #20443]: https://bugs.ruby-lang.org/issues/20443
[Feature #20497]: https://bugs.ruby-lang.org/issues/20497
[Feature #20624]: https://bugs.ruby-lang.org/issues/20624
[Feature #20775]: https://bugs.ruby-lang.org/issues/20775
[Feature #20855]: https://bugs.ruby-lang.org/issues/20855
