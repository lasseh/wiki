
== Personal Linux shell setup ===

=== Programms ===

  apt-get update
  apt-get upgrade
  apt-get install vim screen tmux zsh htop git-core mtr

=== dotfiles ===

  git clone https://github.com/lasseh/dotfiles-server.git .dotfiles
  cd .dotfiles
  perl mklinks.pl --dotfiles
  chsh -s /bin/zsh

=== Git ===



=== Perl Modules ===

  WWW::Mechanize
  DBI
  Net::SNMP
  CPAN::DistnameInfo
  Net::RabbitMQ

