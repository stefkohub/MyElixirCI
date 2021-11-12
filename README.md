# elixir-starting-point

This is a base project for setting up a GitHub Actions-based Elixir
continuous integration (CI) system.

It builds on [the Hashrocket post](https://hashrocket.com/blog/posts/build-the-ultimate-elixir-ci-with-github-actions)
on setting up the ultimate Elixir CI, with a couple important tweaks:

1. It runs the actions on all PRs (you'll want to set up branch protection rules to
   make sure the tests actually pass before merging!)
2. It separates out the CI jobs that don't depend on each other. Thus, if your code
   fails the format check, you'll still get feedback on whether it passed the tests,
   Credo, and Dialyzer, etc. 

The three independent CI jobs it sets up are:

1. Test (via ExUnit)
2. Dialyzer
3. Formatting and Credo (these could be separate tasks, but in my experience they run
   fast enough even on large projects that it's not worth breaking them apart)

We cache as much as we can between the jobs, so for instance your first run of Dialyzer
will be sloooooooow, but subsequent ones should be faster. Do note that running the tasks
in parallel, rather than sequentially, means you're trading CI time for dev productivity.
I can't imagine an engineering team where this is the wrong tradeoff, but your mileage
may vary.

The important bits here for including in your project are:

- `.github/`
- `config/.credo.exs` (though do note that I've made some opinionated choices here)
- `.formatter.exs` (opinionated choice: a very long max line length)
- `mix.exs`, or at least the dependencies, Dialyzer config, and ExCoveralls config there
- Optional: `.dialyzer_ignore.exs` (otherwise you'll need to remove that line from `mix.exs`)
- Optional: `test.sh` for devs to run locally before committing (you could wire this
  up as a pre-commit hook if it's fast enough)
- Optional: `.idea/` if you use Jetbrains Intellij as your IDE (this gets you run
  configurations for formatting & ExUnit tests)
