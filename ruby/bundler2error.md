# Fix Bundler 2 Error
When you see `You must use Bundler 2 or greater with this lockfile.` error while running ruby code, do the following:
- gem update --system
- gem install bundler
- bundle update --bundler
