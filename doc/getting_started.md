#Getting Started

The only requirement is [Sass](http://sass-lang.com), but Susy was built to be part of the [Compass](http://compass-style.org/) ecosystem, and we recommend pairing with tools like [Breakpoint](http://breakpoint-sass.com/) and[ Vertical Rhythms](http://compass-style.org/reference/compass/typography/vertical_rhythm/).

Compass is still required for the [Susy One](doc/susy_one.md) syntax.


## Simple Install

```
# command line
gem install susy
```

## Bundler or Rails

>Warning

>In order to use Susy 2 with Rails you must update your Gemfile to use sass-rails ~> 5.0.0.beta1. This is because Susy 2 requires Sass >= 3.3 whilst Rails 4.1 and below include a version of sass-rails which does not support Sass 3.3. As you can see the sass-rails requirement is still in beta so proceed with a drink in hand.

```
# Gemfile
# gem 'sass-rails', '~> 4.0.3'
gem 'sass-rails', '~> 5.0.0.beta1'
gem 'susy'

# If you want Compass:
gem 'compass-rails', '~> 2.0.0'
```
```
# config/application.rb
require 'susy'
```

```
# command line
bundle install
```

If you add Susy to an exsisting Rails app, follow the steps above, but use bundle update instead of bundle install.

```
# command line
bundle update
```

## Compass

If you want to use Susy with Compass, start by installing Compass.

Create a new Compass project:

```
# command line
compass create --using susy <project name>
```

Alternatively, add Susy to a current project

```
# command line
compass install susy
```

## Bower

```
# command line
bower install susy --save
```

This will add the Susy repository to your bower_components directory or create a bower_components directory for you.

You can also import Susyone.

## Grunt (and Yeoman)
You can enable Susy in Grunt by adding a line to your ```Gruntfile.js``` . You will need to add a line to either your Sass task or, if you’re using Compass, your Compass task.

To add Susy to the Sass task, edit your Gruntfile.js at the root level of your project and look for the Sass-related rules. Add ```require: 'susy'``` inside the ```options``` object:

```
// Gruntfile.js
sass: {
  dist: {
    options: {
      style: 'expanded',
      require: 'susy'
    },
    files: {
        'css/style.css': 'scss/style.scss'
    }
  }
}
```

Assuming you’ve already installed Susy, it will now be added to the project and will not clash with Yeomans grunt rules.

To add Susy to the Compass task, edit your Gruntfile.js at the root level of your project and look for the Compass-related rules. Add ```require: 'susy'``` inside the ```options``` object:

```
// Gruntfile.js
compass: {
    options: {
      require: 'susy',
      ...
    }
  }
}
```

Again, assuming you’ve already installed Susy, it will now be added to the project.

## Manual Start

If you want to copy in the Sass files directly, and skip any package management, you can do that too.

* Download the zip file from GitHub.
* Copy the contents of the “sass” folder (feel free to remove everything else).
* Paste the files in your project “sass” folder (whatever you call it).

## Version Management

When you work with bundled gems across a number of different projects, managing gem versions can become an issue.

If you are in a Ruby environment, check out [RVM](https://rvm.io/). In a Python environment, we recommend [virtualenv](http://www.virtualenv.org/en/latest/index.html) with these [scripts](https://gist.github.com/1078601) added to your “postactivate” and “predeactivate” files.

Once you have that in place, [Bundler](http://bundler.io/) can be used in either environment to manage the actual installation and updating of the gems.

## Quick Start
Once you have everything installed, you can import Susy into your Sass files.

```
@import "susy";
```

The basic Susy layout is composed using two simple mixins:

```
@include container; // establish a layout context
@include span(<width>); // lay out your elements
```

For example:

```
body { @include container(80em); }
nav { @include span(25%); }
```

If you want to lay your elements out on a grid, you can use the span mixin to calculate column widths:

```
nav { @include span(3 of 12); }
```

But you don’t have to do things the Susy way. We give you direct access to the math, so you can use it any way you like:

```
main {
  float: left;
  width: span(4);
  margin-left: span(2) + gutter();
  margin-right: gutter();
}
```

You can also establish global settings, to configure Susy for your specific needs. Create a $susy variable, and add your settings as a map.

```
$susy: (
  columns: 12,  // The number of columns in your grid
  gutters: 1/4, // The size of a gutter in relation to a single column
);
```

There are many more settings available for customizing every aspect of your layout, but this is just a quick-start guide. Keep going to get the details.