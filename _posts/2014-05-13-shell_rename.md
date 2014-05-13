---
layout: post
title: "批量重命名文件——rename"
description: ""
category: ""
tags: []
---
{% include JB/setup %}

从前的代码文件都是以xxx_main.cpp形式命名的，这样的好处是一看就知道哪个模块，但是如果新做个项目的时候也会有问题，需要把文件批量改名。
当然既然可以通用，就不要来回copy，应该封装成为组建，只要每次填写个配置，或者把参数修改下就能有新的表现。这个问题后面再研究，今天
先说下眼下的解决方案。

可以写个脚本，遍历目录下的每个文件，然后用sed匹配，得到目标文件的名字，拼接成shell的mv后再执行。

```
for var in `ls`; do  mv $var `echo "$var" |sed 's/\(.*\)\(_.*\)/abc\2/'`; done
```

但是发现定制性比较强，于是搜了下，发现真有个rename命令，可以解决这个问题。
例如把abc_main.cpp abc_do.cpp abc_done.cpp都改成bcd前缀的，只要执行

```
rename abc bcd *.cpp
```
命令就可以了。
rename的第二个参数时from，第三个参数时to，把第二个的部分更换为第三个的部分，对于最后一个参数罗列的文件范围。
而且看man信息，还是keneral里的函数。

但是回家后发现，再ubuntu里也有个rename，用法还有些不同，时perl写的。
如果用perl版的rename则为 
```
rename s/abc/bcd *.cpp
```
相对来说更强大一点，能支持正则表达式，看了下源代码，也不复杂，写的不错。附在本文的最后。

所以再使用rename的时候还要看下系统到底用的哪个版本才可以。

话说回来，再平时做工程的时候，要使用已有的工具，相对来说bug和成熟度，通用性都比较强，能够省不少力。就像第一个sed的版本，如果要换成efg呢，
还要修改，也可以做成个参数，但是你是不是要检查下呢，一个小工具要做好也时不容易的。

但是从学习的角度来说，要明白其中的原理，即使有现成的工具也要学习。因为目前只是解决了眼下的问题，如果遇到没有现成方案的时候就要自己出马了，这是就
拼功力了。例如：不是重命名了，而是copy出一份来，那么只有使用第一个版本把mv换成cp就ok了。
或者用第二个或第三个的源代码稍加修改也可以。

其实第三个已经很好了，但是是一种新的语言，不一定都会，谁让你不会呢，学呗！

[这里](http://docstore.mik.ua/orelly/perl/cookbook/ch09_10.htm#ch09-40514)也有perl programming里的示例。

```
#!/usr/bin/perl -w
#
#  This script was developed by Robin Barker (Robin.Barker@npl.co.uk),
#  from Larry Wall's original script eg/rename from the perl source.
#
#  This script is free software; you can redistribute it and/or modify it
#  under the same terms as Perl itself.
#
# Larry(?)'s RCS header:
#  RCSfile: rename,v   Revision: 4.1   Date: 92/08/07 17:20:30 
#
# $RCSfile: rename,v $$Revision: 1.5 $$Date: 1998/12/18 16:16:31 $
#
# $Log: rename,v $
# Revision 1.5  1998/12/18 16:16:31  rmb1
# moved to perl/source
# changed man documentation to POD
#
# Revision 1.4  1997/02/27  17:19:26  rmb1
# corrected usage string
#
# Revision 1.3  1997/02/27  16:39:07  rmb1
# added -v
#
# Revision 1.2  1997/02/27  16:15:40  rmb1
# *** empty log message ***
#
# Revision 1.1  1997/02/27  15:48:51  rmb1
# Initial revision
#

use strict;

use Getopt::Long;
Getopt::Long::Configure('bundling');

my ($verbose, $no_act, $force, $op);

die "Usage: rename [-v] [-n] [-f] perlexpr [filenames]\n"
    unless GetOptions(
	'v|verbose' => \$verbose,
	'n|no-act'  => \$no_act,
	'f|force'   => \$force,
    ) and $op = shift;

$verbose++ if $no_act;

if (!@ARGV) {
    print "reading filenames from STDIN\n" if $verbose;
    @ARGV = <STDIN>;
    chop(@ARGV);
}

for (@ARGV) {
    my $was = $_;
    eval $op;
    die $@ if $@;
    next if $was eq $_; # ignore quietly
    if (-e $_ and !$force)
    {
	warn  "$was not renamed: $_ already exists\n";
    }
    elsif ($no_act or rename $was, $_)
    {
	print "$was renamed as $_\n" if $verbose;
    }
    else
    {
	warn  "Can't rename $was $_: $!\n";
    }
}

__END__

=head1 NAME

rename - renames multiple files

=head1 SYNOPSIS

B<rename> S<[ B<-v> ]> S<[ B<-n> ]> S<[ B<-f> ]> I<perlexpr> S<[ I<files> ]>

=head1 DESCRIPTION

C<rename>
renames the filenames supplied according to the rule specified as the
first argument.
The I<perlexpr> 
argument is a Perl expression which is expected to modify the C<$_>
string in Perl for at least some of the filenames specified.
If a given filename is not modified by the expression, it will not be
renamed.
If no filenames are given on the command line, filenames will be read
via standard input.

For example, to rename all files matching C<*.bak> to strip the extension,
you might say

	rename 's/\.bak$//' *.bak

To translate uppercase names to lower, you'd use

	rename 'y/A-Z/a-z/' *

=head1 OPTIONS

=over 8

=item B<-v>, B<--verbose>

Verbose: print names of files successfully renamed.

=item B<-n>, B<--no-act>

No Action: show what files would have been renamed.

=item B<-f>, B<--force>

Force: overwrite existing files.

=back

=head1 ENVIRONMENT

No environment variables are used.

=head1 AUTHOR

Larry Wall

=head1 SEE ALSO

mv(1), perl(1)

=head1 DIAGNOSTICS

If you give an invalid Perl expression you'll get a syntax error.

=head1 BUGS

The original C<rename> did not check for the existence of target filenames,
so had to be used with care.  I hope I've fixed that (Robin Barker).

=cut

```
