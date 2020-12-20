---
layout: post
title: "Writing Ruby gems for different versions of Ruby and Rails"
date: 2020-12-19 09:05:06 +0000
comments: true
categories: 
---

This article gives a quick overview of how gem versions are managed in Ruby applications and then explains how to correctly version your own gems and their dependencies. 

<!--more-->

## Ways your gem is consumed
           
Before going over how to version your Ruby gems and their dependencies, it's first worth covering the two ways your gem may be consumed: as part of another gem, or as part of a Ruby application. Each uses a slightly different mechanism.

### Other Ruby gems

For each version of Ruby installed on your system, there will be one or more versions of the Ruby Gems command line interface installed. This utility has two functions it serves:

* As a client for the the Ruby Gems repositories: it allows downloading and installing gems (and their dependencies) from the Ruby Gems servers. It also allows building and publishing gems you may have authored locally and wish to distribute.
* As a package manager: it resolves compatible versions of gems and their dependencies.

The version of Rubygems you're running can be seen with:

```
gem --version
```
       
Each gem includes a `gemspec` file that provides metadata about that gem, including its version and the compatible versions of any gems it may depend on. Ruby gems uses this information to download dependencies whenever the gem is used in a Ruby application. It is by including your gem in another's `gemspec`, that other gems may depend on yours.

Ruby gems will install the latest stable version of each dependency that satisfies the version constraints in the `gemspec` file (while accounting for if there are other dependencies of the gem that also depend on it, and also have version constraints to consider).

To install the latest version of a gem and all of its dependencies:

```
gem install <gem_name>
```

Install a specific version of a gem:

```
gem install <gem_name> -v<version>
```

To see the dependencies of a gem:

```
gem dependency <gem_name> -v<version>
```
     
### Ruby applications

Bundler is a Ruby gem (so it is installed and managed by Ruby Gems) that handles installing the correct gem versions for Ruby _applications_ (differentiated from Ruby Gems, which manages dependencies between Ruby _gems_ directly). 

The version of bundler can be seen by running:

```
bundle --version
```

Bundler takes the list of gems a Ruby application depends on in the form of a `Gemfile` (which may or may not specify version constraints) and resolves a version of each gem to use. It does so by starting from the latest stable version of each gem, and then applying any constraints specified in the `Gemfile`; it then applies the version constraints of any _other_ gems in the `Gemfile` that depend on it (if applicable). This process is done recursively, to ensure the gems listed in the `Gemfile`, and all of their dependencies (and all of _their_ dependencies, etc) are compatible with one another. 

It saves that list of specific versions as a `Gemfile.lock` file, which is used for all future bundle installs, rather than attempting to re-calculate the bundle of compatible gems again. This means versions of the gems that are subsequently published are _not_ installed on future runs of `bundle install`.

To install the same versions of all gems as when the bundle was built (or to build the bundle for the first time if a `Gemfile.lock` is not present or new gems have been added to `Gemfile`):

```
bundle install
```
 
To recalculate the bundle (still sticking to the version constraints mentioned in the `Gemfile`):

```
bundle update
```

To update beyond the versions that are mentioned in your `Gemfile`, you must first update the `Gemfile` and then run `bundle update`.

To get a list of gems in the bundle that can be updated to newer versions (ignoring any version constraints of other gems that depend on it):

```
bundle outdated
```
           
### Differences between Ruby Gems and Bundler

The Ruby Gems CLI does not keep a record of the exact versions of gem's dependencies it installs (it does maintain a local cache, though). `gemspec` files should list the full range of known compatible dependency versions, to be as permissive as possible (without resorting to optimistic versioning). 

Bundler, however, takes the opposite approach to manage Ruby application's dependencies with `Gemfile.lock`: the list of gem versions are as restrictive as possible, to ensure the same behaviour between installs of the Ruby application (without the chance of different dependency versions being used). This is why the `Gemfile.lock` should be committed with version control, and shared between development and production deployments of the Ruby application.

Bundler can also used to manage the development and testing of gems: usually you want to ensure the same version of your test framework gems and other development dependencies are used between (development) installs of the gem.

Because it's possible to reference the contents of a `gemspec` file from a `Gemfile`, the `Gemfile` included with the gem is usually just a wrapper for the `gemspec`: 

```ruby
source 'https://rubygems.org'

gemspec
```

However, it allows you to install the development dependencies using `bundle install`, which (importantly) generates a `Gemfile.lock`, and if committed to version control, ensures anyone else who works on the gem or its test suite will get the same versions of the development dependencies. The `Gemfile` and `Gemfile.lock` are ignored when downloading the gem through Ruby Gems; they'e only used during development.

## Establishing what versions of Ruby your gem is compatible with
             
### Decide up-front

Ideally, you should decide what versions of Ruby you're going to support before you begin writing your gem.

You can use [RubyGem stats](https://stats.rubygems.org/) to find out the latest Ruby version usage statistics, to make an informed decision about what versions you would like to support (and thereby what approximate percentage of Ruby projects or users).

If you're still in doubt, you can check popular, well maintained gems for what versions or Ruby they are still supporting, and use it as a guide.

You'll also need to consult the supported versions of Ruby of any dependencies you add to your gem, as they may further limit which ones your gem can support in turn. You will need to check if the list of supported Ruby versions _changes_ over the versions of the gem yours is compatible with. Generally when a well-maintained gem drops support for a version of Ruby, it is done so in a major increment (but unfortunately, not always).
                                                                          
To get the required Ruby versions of a gem yours depends on (in YAML): 

```
gem specification <dependency-gem-name> -v <version> required_ruby_version
```
              
### Check your code for version-specific features

Either you will have decided what versions of Ruby you're supporting up-front, and will restrict yourself to the syntax and standard library calls that are available in all versions, or you will have already written or inherited a gem that you need to audit. In either case, when you have finished your gem, it's a good idea to go through and check all classes and methods of the standard library are available in all versions of Ruby you're supporting.
                                   
You can use [API Dock's Ruby search](https://apidock.com/ruby) to search for classes and methods, and [use the bar graph](https://apidock.com/faq#What-is-the-bar-graph-on-the-top-of-documentation-pages) to determine in what versions of Ruby they were available, and when their behaviour may have changed. 

Checking changes in syntax and idioms is a little trickier. You can use a summary of new feature with each new version (like [Ruby Changelog.com](https://rubychangelog.com/) or [Ruby Guides](https://www.rubyguides.com/ruby-version-changes/)) to get an idea. However, a more active scan can be done by installing [Rubocop](https://github.com/rubocop-hq/rubocop) and by setting the `TargetRubyVersion` in the `.rubocop` file to your lowest supported Ruby version which, as well as performing other code linting, can warn against syntax and standard library calls not available in the target Ruby version:

```yaml
AllCops:
  TargetRubyVersion: 2.0.0
```
  
### Test against different Ruby versions

Finally, you can use [rvm](https://rvm.io/) (or similar) to switch between Ruby versions for testing applications or running your gem's test suite.
                       
### Declaring version-specific code

You can determine the version of Ruby at runtime using the `RUBY_VERSION` constant:

```ruby
if RUBY_VERSION.start_with?('2.') 
  # ...
end
```

Because `RUBY_VERSION` is a string and difficult to compare, if you're only supporting [Ruby versions beyond 1.9.3](https://apidock.com/ruby/Gem/Version), you can wrap it in `Gem::Version`:

```ruby
if Gem::Version.new(RUBY_VERSION) >= Gem::Version.new('2.0.0')
  # ...
end
```

This approach may assist with normalizing the differences in behaviour of the standard library versions, but will not polyfill newer Ruby language syntax or idioms into older versions of Ruby.

### Declaring what versions of Ruby your gem supports

To declare the versions of Ruby your gem will work with, use the [required_ruby_version](https://guides.rubygems.org/specification-reference/#required_ruby_version) option in its `gemspec` file:

```ruby
Gem::Specification.new do |s|
  # ...                          
  # Compatible with any version of Ruby starting with a 2. (excluding pre-releases)
  s.required_ruby_version '~> 2.0'
end
```

Generally you'll want to be using _pessimistic_ version constraints (as opposed to _optimistic_ ones), which means you'll want to put an upper bound on the version of (likely future) versions of Ruby that are known to work with your gem: e.g. `~> 2.0` rather than `>= 2.0.0`. This means when Ruby 3.0 comes out, you will need to publish a new version of the gem, explicitly stating it's compatible, rather than assuming your gem will be compatible with any future version of Ruby. 

The `~>` operator is shorthand for fixing all but the last number mentioned: e.g. `~> 2.0` is shorthand for `['>=2.0.0', '< 3.0']`. We fix to the major version of Ruby as it's unlikely any breaking changes will be introduced without a major increment.

If you want to support different major versions of Ruby, you'll need to use the longer _compound requirement_ format: e.g. `['>=1.7', '< 3']` These values ANDed, i.e. all version expressions must be true of a version to be compatible.

## Establishing what versions of Rails (or other dependencies) your gem is compatible with

This section uses writing a Rails engine as an example, but the same applies for writing any gem with a dependency on another.

### Decide up-front

Once again, ideally you will know this up-front and restrict your gem to only using the public methods available for those versions. Go through your code at the end of writing and make sure all classes and methods you're using are available in all versions of the gem you're supporting.

To get a list of available versions of a gem:

```
gem list --remote -e <dependency-name>
```

### Check your code for version-specific features

For Rails, you can search through [API Docks.com](https://apidock.com/rails) and [use the bar graph](https://apidock.com/faq#What-is-the-bar-graph-on-the-top-of-documentation-pages). For other open source gems, you will need to [check the source code on Github](/blog/2020/12/19/tracking-changes-in-open-source-libraries-on-github.markdown).

Similar to the above, the [Rubocop Rails plugin](https://github.com/rubocop-hq/rubocop-rails) allows you to target the lowest version of Rails you're planning on supporting and warn of missing or incompatible functionality:

```yaml
AllCops:
  TargetRailsVersion: 4.0
```

This is likely to be less effective than the linting for different Ruby versions, as this feature started out its life as a way to toggle off newer linting rules that don't apply to older versions of Rails, rather than to warn of newer methods and classes being used with older versions of Rails.

### Test against different gem versions

To actively test a gem's Rails dependency, you can generate a series of test Rails applications [using different versions](https://stackoverflow.com/questions/379141/specifying-rails-version-to-use-when-creating-a-new-application) to verify it behaves as expected. 

To execute a test suite against a dummy app, you may choose to symlink the dummy app to those different Rails applications, or use a git submodule with a different Rails version on each branch, that you can switch between. You can use the approach for generating applications using different versions of Rails, but [generate a rails engine instead](https://stackoverflow.com/a/23512768/1337796) and copy its dummy application (only) over into your git submodule.

To test your gem against other dependencies, modify the `add_runtime_dependency` in your gem's `gemspec` file, to be specific versions, `bundle install` and re-run your test suite to make sure it still passes. Just remember to set it back before committing to version control or publishing your gem.

### Declaring version-specific code 
                                     
Since Rails 1.0, it has provided a `VERSION` constant, which contains: `MAJOR`, `MINOR`, `TINY` and `PRE` values. The first 3 are integers, while `PRE` is a string.

These can be used to provide Rails-specific behaviour, to allow your gem to support versions of Rails with different interfaces, at the same time.

```ruby
if Rails::VERSION::MAJOR >= 4
  # Rails 4 (and above) compatible behaviour
else
  # Before Rails 4 compatible behaviour
end
```
   
For other gems, you can use the following approach (although I'd suggest doing so, sparingly):

```ruby
if Gem.loaded_specs['gem_name'].version >= Gem::Version.new('2.0.0')
  # ...
end
```

### Declaring what versions of Rails your gem is compatible with
             
To declare a runtime dependency (a gem yours depends on at runtime, not just during development or testing), add to your gem's `gemspec` file:

```ruby
Gem::Specification.new do |s|
  # ...                          
  # Compatible with any version of <gem_name> starting with a 2. (excluding pre-releases)
  s.add_runtime_dependency 'gem_name', '~> 2.0'
end
```
           
Again, pessimistic versioning is recommended (although keeping on top of when any of your dependencies have new versions available is a much larger effort than doing so for versions of Ruby). 
             
To get a summary of dependencies that may be outdated, you can regularly run in the root of your gem:

```
bundle outdated
```
        
## How to version your gem when compatible Ruby or dependency versions change 
                    
The approach to how you should version your gem in relation to changes in the dependency versions it supports, depends on whether you are adding a version (or expanding the list of compatible versions) or removing them.

If you need to indicate compatibility with a new major version of a dependency (assuming you're using pessimistic versioning based on major versions), it should be done in isolation (and not bundled up with any other changes) as a minor or patch version, so consumers of your gem can upgrade without any undue friction. You must publish a new version because you cannot change the `gemspec` file for a published gem without giving it a new version. It should not mean any change in behaviour of your gem (and therefore should not constitute a major increment) and choosing a minor increment affords you the option of patching it (with high priority security of bug fixes) if you need to in the future (without requiring users to perform larger upgrades to get those patches).

If you are dropping support for a version or range of versions, this should be done as a major increment. Even if doing so does not change the behaviour of your gem, it will likely mean larger changes in the applications that consume it (such as the need to upgrade the bundle). The size and complexity of this upgrade should be communicated to users of your gem by the major increment. For this reason, dropping support for older versions of dependencies is often bundled in with major changes to the library, to reduce the number of major upgrades consumers must go through to stay up-to-date. 

The exception to this may be if you discover there is a bug in a particular version of a dependency, which you want to warn users about by labelling it as being incompatible. This is usually done as a patch increment. For example:


```ruby
Gem::Specification.new do |s|
  # ...                          
  # All versions 2.* of gem_name, as long as they include the bugfix implemented in 2.1
  s.add_runtime_dependency 'gem_name', '~> 2.0', '>= 2.1'
end
```
                               
## Further reading

For additional important concepts on how to manage and publish gems that are well versioned, see [RubyGem's Patterns](https://guides.rubygems.org/patterns/).
