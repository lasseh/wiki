### Linux sysctl security

```
...
# Do not accept ICMP redirects (prevent MITM attacks)
net.ipv4.conf.all.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0
# _or_
# Accept IC

# Uncomment the next two lines to enable Spoof protection (reverse-path filter)
# Turn on Source Address Verification in all interfaces to
# prevent some spoofing attacks
net.ipv4.conf.default.rp_filter=1
net.ipv4.conf.all.rp_filter=1

...

# Do not accept ICMP redirects (prevent MITM attacks)
net.ipv4.conf.all.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0
# _or_
# Accept ICMP redirects only for gateways listed in our default
# gateway list (enabled by default)
# net.ipv4.conf.all.secure_redirects = 1
#
# Do not send ICMP redirects (we are not a router)
net.ipv4.conf.all.send_redirects = 0
#
# Do not accept IP source route packets (we are not a router)
net.ipv4.conf.all.accept_source_route = 0
net.ipv6.conf.all.accept_source_route = 0
```



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

