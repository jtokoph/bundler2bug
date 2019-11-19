# How to repoduce

Make sure you have bundler 2 installed.

```shell
# Clone this repo
git clone https://github.com/jtokoph/bundler2bug.git

cd bundler2bug

# Confirm that the bundled with is 1.17.3
grep -A 1 'BUNDLED WITH' subdir/Gemfile.lock
# BUNDLED WITH
#    1.17.3

# Go into the subdirectory and run bundle update pry
cd subdir

bundle update pry

# Note that bundled with is unchanged
grep -A 1 'BUNDLED WITH' subdir/Gemfile.lock
# BUNDLED WITH
#    1.17.3

# Now go back to the parent directory, reset the lockfile and run bundle update again
cd ..
git checkout -- subdir/Gemfile.lock
bundle update pry
grep -A 1 'BUNDLED WITH' subdir/Gemfile.lock
# BUNDLED WITH
#    2.0.2

# This is wrong
# When a repo includes something like:
#  BUNDLE_GEMFILE: "subdir/Gemfile"
# Bundler correctly finds the Gemfile but does not auto-switch to v1
# Bundler does correctly auto-switch to v1 if the value is set an env var

git checkout -- subdir/Gemfile.lock
BUNDLE_GEMFILE="subdir/Gemfile" bundle update pry
grep -A 1 'BUNDLED WITH' subdir/Gemfile.lock
# BUNDLED WITH
#    1.17.3
