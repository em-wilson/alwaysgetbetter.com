# alwaysgetbetter.com

This is a Jekyll-powered blog.

## Getting Started

To get this project up and running locally, follow these steps:

### 1. Install Ruby and Bundler

This project requires Ruby and Bundler. It is highly recommended to use a Ruby version manager (like `rbenv` or `asdf`) to manage your Ruby installations and avoid conflicts with your system Ruby.

If you are using `asdf`, ensure you have it set up for Ruby.

### 2. Install Project Dependencies

Once Ruby is set up, navigate to the project root and install the required gems using Bundler:

```sh
bundle install
```

**Note on `libyaml` errors:** If you encounter `LoadError: Library not loaded: /opt/homebrew/opt/libyaml/...` issues, this typically means your Ruby installation's `psych` gem cannot find its `libyaml` dependency. To fix this:

1.  Ensure `libyaml` is installed via Homebrew: `brew install libyaml`
2.  Reinstall your Ruby version (e.g., `asdf uninstall ruby 3.3.0 && asdf install ruby 3.3.0` for Ruby 3.3.0).
3.  Then run `bundle install` again.

### 3. Run the Project

After installing the dependencies, you can serve the Jekyll site locally:

```sh
bundle exec jekyll serve
```

This command will build your site and make it available at `http://localhost:4000` (or another port if 4000 is in use).