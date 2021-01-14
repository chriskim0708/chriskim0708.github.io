## Install Guide for MacOS
1. command line tool이 미설치시 실행
```
xcode-select --install
```
2. Homebrew 미설치시 실행
```
# Install Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
3. Ruby 설치
```
brew install ruby
```
4. 터미널 환경변수 설정
```
# 사용하는 shell 확인
echo $SHELL

# If you're using Zsh
echo 'export PATH="/usr/local/opt/ruby/bin:$PATH"' >> ~/.zshrc

# If you're using Bash
echo 'export PATH="/usr/local/opt/ruby/bin:$PATH"' >> ~/.bash_profile
```
5. Ruby가 정상적으로 설치됐는지 확인
```
which ruby
# /usr/local/opt/ruby/bin/ruby

ruby -v
ruby 2.7.2p137 (2020-10-01 revision 5445e04352)
# 버전은 다를 수 있음
```
6. jekyll install
```
gem install --user-install bundler jekyll
```
7. jekyll 터미널 환경변수 설정
```
# ruby 버전을 우선 확인
ruby -v

# X.X.0 위치에 ruby 버전을 입력하여 환경변수 설정
# If you're using Zsh
echo 'export PATH="$HOME/.gem/ruby/X.X.0/bin:$PATH"' >> ~/.zshrc

# If you're using Bash
echo 'export PATH="$HOME/.gem/ruby/X.X.0/bin:$PATH"' >> ~/.bash_profile
```
8. 프로젝트 root 폴더에서 install 및 build 실행
  - Install the theme: `bundle install`
  - Build your site: `bundle exec jekyll serve`