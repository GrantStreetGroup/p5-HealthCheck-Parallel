# NAME

HealthCheck::Parallel - A HealthCheck that uses parallelization for running checks

# VERSION

version v0.0.1

# SYNOPSIS

    use HealthCheck::Parallel;

    my $hc = HealthCheck::Parallel->new(
        max_procs  => 4,      # default
        tempdir    => '/tmp', # override Parallel::ForkManager default
        child_init => sub { warn "Will run at start of child process check" },
        checks     => [
            sub { sleep 5; return { id => 'slow1', status => 'OK' } },
            sub { sleep 5; return { id => 'slow2', status => 'OK' } },
        ],
    );

    # Takes 5 seconds to run both checks instead of 10.
    my $res = $hc->check;

    # These checks will not use parallelization.
    $res = $hc->check( max_procs => 0 );

# DESCRIPTION

This library inherits [HealthCheck](https://metacpan.org/pod/HealthCheck) so that the provided checks are run in
parallel.

# METHODS

## new

Overrides the ["new" in HealthCheck](https://metacpan.org/pod/HealthCheck#new) constructor to additionally allow a
["max\_procs"](#max_procs) argument for the maximum number of checks/processes to run in
parallel.

# ATTRIBUTES

## max\_procs

A positive integer specifying the maximum number of processes that should be run
in parallel when executing the checks.
No parallelization will be used unless given a value that is greater than 1.
Defaults to 4.

## child\_init

An optional coderef which will be run when the child process of a check is
created.
A possible important use case is making sure child processes don't try to make
use of STDOUT if these checks are running under FastCGI envrionment:

    my $hc = HealthCheck::Parallel->new(
        child_init => sub {
            untie *STDOUT;
            { no warnings; *FCGI::DESTROY = sub {}; }
        },
    );

## tempdir

Sets the `tempdir` value to use in [Parallel::ForkManager](https://metacpan.org/pod/Parallel%3A%3AForkManager) for IPC.

# DEPENDENCIES

- Perl 5.10 or higher.
- [HealthCheck](https://metacpan.org/pod/HealthCheck)
- [Parallel::ForkManager](https://metacpan.org/pod/Parallel%3A%3AForkManager)

# SEE ALSO

- [HealthCheck::Diagnostic](https://metacpan.org/pod/HealthCheck%3A%3ADiagnostic)
- The GSG
[Health Check Standard](https://grantstreetgroup.github.io/HealthCheck.html).

# AUTHOR

Grant Street Group <developers@grantstreet.com>

# COPYRIGHT AND LICENSE

This software is Copyright (c) 2023 by Grant Street Group.

This is free software, licensed under:

    The Artistic License 2.0 (GPL Compatible)
