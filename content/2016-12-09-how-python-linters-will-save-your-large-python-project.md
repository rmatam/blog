title: How Python Linters Will Save Your Large Python Project
date: 2016-12-09 08:53
categories: python lint pep8 pep257

A Python project I'm working on at [Enigma](http://enigma.io) is starting to grow rather large. I spent a good deal of
effort yesterday getting a five line change added to our `Makefile` (which is run as part of our CI and CD pipeline on
every pull request and merge). After the PR was merged, I gloated to others how awesome my team's project was. Here are
the five lines:

```
lint:
	pylint --rcfile=.pylintrc api -f parseable -r n && \
	mypy --silent-imports api && \
	pycodestyle api --max-line-length=120 && \
	pydocstyle api
```

For completeness sake, here is the `test` target that actually gets run as part of CI tests:

```
test: install lint
	python3 setup.py test --addopts="--cov=api"
	coverage xml -i -o coverage/cobertura-coverage.xml --omit=$(VIRTUAL_ENV)/*,.eggs/*
```

(So, for those not versed in "Makefile-ese", the `test` target won't run successfully if the `lint` target doesn't do so
first).

Why was I happy enough to be a jerk to other engineers and brag about those five lines? It all comes down to *complexity*.

<!--more-->

As Python projects grow, maintenance becomes a nightmare (I'm more referring to enterprise-sized projects rather than
"large" personal projects, but the same idea holds). Code becomes disorganized, messy, reflects the style of the author
(even for teams doing a decent job enforcing [PEP-8](https://www.python.org/dev/peps/pep-0008/) and
[PEP-257](https://www.python.org/dev/peps/pep-0257/), and docstrings fall by the wayside. It takes new developers longer
and longer to ramp up on the project. Simple fixes and feature changes become not so simple when it comes time to
actually make them. All of these are due to the necessary increase in complexity as a project grows. So how to we reduce
that complexity as much as possible?

## No Panacea

Obviously, there's no one answer here, but there's a set of actions you can take *right now* which requires low overhead and pays huge dividends:

**Make code "linters" part of your build process. If *any* of them report *any* issues, fail your build.**

So what is a code "linter"?

> A code "linter" is a tool that performs *static analysis* on your code and reports coding style violations, possible
> design flaws, outright bugs, and code patterns known to be dangerous. *Static analysis* means that your code is
> analyzed *but not executed*, so there are no external dependencies and running a linter will never harm your
> environment. You can think of them as a poor-man's AI code reviewer.

Linters have various output formats and modes they can be run in (and vary in complexity and usefulness). Here's
example output from [pylint](https://www.pylint.org), one of the most popular Python linters:

    $ pylint sandman2
    ************* Module sandman2.app
    C:117, 0: Line too long (81/80) (line-too-long)
    C:163, 0: Line too long (88/80) (line-too-long)
    C: 24, 0: Invalid constant name "auth" (invalid-name)
    R: 27, 0: Too many arguments (6/5) (too-many-arguments)
    ************* Module sandman2.decorators
    W: 42,24: Duplicate key 'status' in dictionary (duplicate-key)
    ************* Module sandman2.model
    C: 49, 0: Line too long (89/80) (line-too-long)
    C: 50, 0: Line too long (113/80) (line-too-long)
    C:141, 0: Invalid constant name "DeclarativeModel" (invalid-name)
    C: 10, 0: Imports from package sqlalchemy are not grouped (ungrouped-imports)
    ************* Module sandman2.service
    C: 98, 0: Trailing whitespace (trailing-whitespace)
    C:208, 0: Line too long (87/80) (line-too-long)
    C:215, 0: Line too long (93/80) (line-too-long)
    C:224, 0: Line too long (82/80) (line-too-long)
    R:231, 4: Method could be a function (no-self-use)
    ************* Module sandman2.scripts.sandman2ctl
    C: 40, 0: Line too long (98/80) (line-too-long)
    
    
    Report
    ======
    364 statements analysed.
    
    Statistics by type
    ------------------
    
    +---------+-------+-----------+-----------+------------+---------+
    |type     |number |old number |difference |%documented |%badname |
    +=========+=======+===========+===========+============+=========+
    |module   |9      |9          |=          |100.00      |0.00     |
    +---------+-------+-----------+-----------+------------+---------+
    |class    |12     |12         |=          |100.00      |0.00     |
    +---------+-------+-----------+-----------+------------+---------+
    |method   |21     |21         |=          |100.00      |0.00     |
    +---------+-------+-----------+-----------+------------+---------+
    |function |18     |18         |=          |100.00      |0.00     |
    +---------+-------+-----------+-----------+------------+---------+
    
    
    
    External dependencies
    ---------------------
    ::
    
        flask (sandman2.app,sandman2.decorators,sandman2.service)
          \-views (sandman2.service)
        flask_sqlalchemy (sandman2.model)
        sandman2 (sandman2.scripts.sandman2ctl)
          \-admin (sandman2.app)
          \-app (sandman2)
          \-decorators (sandman2.service)
          \-exception (sandman2.app,sandman2.decorators,sandman2.service)
          \-model (sandman2.app,sandman2.service)
          \-service (sandman2.app)
        sqlalchemy
          \-ext
          | \-automap (sandman2.model)
          | \-declarative (sandman2.model)
          \-inspection (sandman2.model)
          \-sql
            \-sqltypes (sandman2.app)
    
    
    
    Raw metrics
    -----------
    
    +----------+-------+------+---------+-----------+
    |type      |number |%     |previous |difference |
    +==========+=======+======+=========+===========+
    |code      |459    |54.38 |459      |=          |
    +----------+-------+------+---------+-----------+
    |docstring |243    |28.79 |243      |=          |
    +----------+-------+------+---------+-----------+
    |comment   |20     |2.37  |22       |-2.00      |
    +----------+-------+------+---------+-----------+
    |empty     |122    |14.45 |118      |+4.00      |
    +----------+-------+------+---------+-----------+
    
    
    
    Duplication
    -----------
    
    +-------------------------+------+---------+-----------+
    |                         |now   |previous |difference |
    +=========================+======+=========+===========+
    |nb duplicated lines      |0     |0        |=          |
    +-------------------------+------+---------+-----------+
    |percent duplicated lines |0.000 |0.000    |=          |
    +-------------------------+------+---------+-----------+
    
    
    
    Messages by category
    --------------------
    
    +-----------+-------+---------+-----------+
    |type       |number |previous |difference |
    +===========+=======+=========+===========+
    |convention |12     |13       |-1.00      |
    +-----------+-------+---------+-----------+
    |refactor   |2      |2        |=          |
    +-----------+-------+---------+-----------+
    |warning    |1      |3        |-2.00      |
    +-----------+-------+---------+-----------+
    |error      |0      |0        |=          |
    +-----------+-------+---------+-----------+
    
    
    
    % errors / warnings by module
    -----------------------------
    
    +-----------------------------+------+--------+---------+-----------+
    |module                       |error |warning |refactor |convention |
    +=============================+======+========+=========+===========+
    |sandman2.decorators          |0.00  |100.00  |0.00     |0.00       |
    +-----------------------------+------+--------+---------+-----------+
    |sandman2.service             |0.00  |0.00    |50.00    |33.33      |
    +-----------------------------+------+--------+---------+-----------+
    |sandman2.app                 |0.00  |0.00    |50.00    |25.00      |
    +-----------------------------+------+--------+---------+-----------+
    |sandman2.model               |0.00  |0.00    |0.00     |33.33      |
    +-----------------------------+------+--------+---------+-----------+
    |sandman2.scripts.sandman2ctl |0.00  |0.00    |0.00     |8.33       |
    +-----------------------------+------+--------+---------+-----------+
    
    
    
    Messages
    --------
    
    +--------------------+------------+
    |message id          |occurrences |
    +====================+============+
    |line-too-long       |8           |
    +--------------------+------------+
    |invalid-name        |2           |
    +--------------------+------------+
    |ungrouped-imports   |1           |
    +--------------------+------------+
    |trailing-whitespace |1           |
    +--------------------+------------+
    |too-many-arguments  |1           |
    +--------------------+------------+
    |no-self-use         |1           |
    +--------------------+------------+
    |duplicate-key       |1           |
    +--------------------+------------+
    
    
    
    Global evaluation
    -----------------
    Your code has been rated at 9.59/10 (previous run: 9.50/10, +0.08)

Of course, this is *super* verbose, and is definitely the most comprehensive output of any of the linters I've used, but
it gives a good sense of what they look for and what they're capable of. [`pylint`](https://www.pylint.org) provides a
helpful (if *somewhat* arbitrary) "score" at the end of the output which is saved between runs, so you know if your code
is getting better or worse by `pylint` standards.

## So How Does This Relate To Continuous Integration?

In their most basic mode, most linters will print out all violations to `STDOUT` *and return a non-zero exit code if any
violations were encountered*. That means, if everything went perfectly, the command just runs to success with no output.
If you take a look at my `Makefile` change at the top of the article, that's why we can chain together calls to
successive linters and the first one to report a violation will abort the entire set of commands.

By hooking this up to your CI platform, **you can enforce good style, catch bugs, and actually require proper documentation for everything (and I do mean *everything*) at build time.** Someone doesn't follow PEP-8? Build fails. Sorry, your PR can't be merged. Didn't write a docstring for that package/module/class/function? Sorry, build failed. Wrote a function that takes 14 arguments and named it `foo`? You guessed it... **Build failed.**

This is huge for a number of reasons:

* Everyone responsible for doing code reviews doesn't even have to *look* at the PR until the build is passing. You
  actually have to follow all the rules before anyone will read your code.
* Linters are capable of catching *actual bugs* without ever executing your code. It's like another (very small) layer of testing
* New developers and code reviewers can open any file in your project **and the layout, naming conventions,
  documentation, and everything else** is all identical to any other file they might open. The benefit of this can not
  be overstated.

## I'm Convinced! Let's Lint This Puppy!

Python (like most languages) has a number of linters available, all of which do similar (but not necessarily exactly the
same) things. To confuse matters, everyone thinks it's a great idea every once in a while to write a tool that just
packages all the linters that are currently en vogue and fires all of them at your code like a fire hose. But this
happens every few years, so you get meta-linters made of meta-linters, until who knows what tool is actually looking at
your code (and if it's still maintained).

So I'm going to be "that guy" and just tell you what linters you should run. The following list was chosen very
carefully, but I'll get into linter holy wars in the comments if anyone is game.

The linters you should use are:

* [`pylint`](http://www.pylint.org)
* [`pycodestyle`](https://github.com/PyCQA/pycodestyle)
* [`pydocstyle`](https://github.com/PyCQA/pydocstyle)

And that's it. I'll briefly cover each one below.

### pylint

`pylint` has been around forever and is almost certainly the most comprehensive Python linter available. Some complain
that it's too picky or too verbose, but that's what config files are for! One of the lesser-known (awesome) features of
`pylint` is the `.pylintrc` file, which `pylint` will even *create a skeleton with sane defaults for you* if you run

```
$ pylint --generate-rcfile
```

I still marvel that more CLI tools *don't* do this. It's simply awesome.

You can greatly reduce `pylint`'s verbosity by running with the `-r n` flag to suppress that giant report.

### pycodestyle and pydocstyle

Both of these linters used to have different names: the former was called `pep8` and the latter `pep257` (after the PEPs
they check conformance against). In a [somewhat hilarious GitHub issue](https://github.com/PyCQA/pycodestyle/issues/466), GvR himself asked them to kindly change the names of the tools to avoid people hating on [PEP-8](https://www.python.org/dev/peps/pep-0008/) just because the tool of the same name was bugging them. Regardless, these tools are comprehensive and easy to get started with. Just `pip install` them and then run `$ pycodestyle <some-module-or-package-or-file>` and find out everything you've been doing wrong all this time!

As one might expect, `pycodestyle` handles [PEP-8](https://www.python.org/dev/peps/pep-0008/) (the official Style Guide for Python Code) conformance while `pydocstyle` handles [PEP-257](https://www.python.org/dev/peps/pep-0257/) (the official Docstring Conventions for Python). The latter is awesomely strict. When and if you get your project to pass it without error (took me a day on the project in question) you'll never have to worry about someone forgetting to write a docstring (or forgetting *how* to write a docstring) again. It even checks for proper capitalization, punctuation, and prescriptive vs. descriptive comments. I am in love with its pedantry.

## Automate All The Things

In all seriousness, [Enigma](http://www.enigma.io) is a startup. We have limited resources. If we want to keep a large
project sane, the only way to do that is through automation (because we're too busy building [awesome](http://enigma.io/labs/healthcare-data/) [stuff](http://bit.ly/boomtobust) with public data). I would go so far as to argue that, between our testing suite (unit tests and E2E browser tests), continuous integration pipeline (Jenkins + GitHub + Codecov + a lot of homegrown glue), our continuous deployment pipeline (10% Jenkins/GitHub, 90% Ansible), and tools like the linters mentioned above, **we are saving the cost of two full-time engineers to manage our development and deployment processes while making our code base more readable, more testable, and more maintainable.**

All of that from a couple of automated tools you can install in the next five minutes... What are you waiting for!?
