[![Build Status](https://travis-ci.org/ePages-de/Mockify.svg?branch=master)](https://travis-ci.org/ePages-de/Mockify) [![MetaCPAN Release](https://badge.fury.io/pl/Test-Mockify.svg)](https://metacpan.org/release/Test-Mockify)
# Mockify

Test::Mockify - minimal mocking framework for perl

# SYNOPSIS

    use Test::Mockify;
    use Test::Mockify::Verify qw ( WasCalled );
    use Test::Mockify::Matcher qw ( String );

    # build a new mocked object
    my $MockObjectBuilder = Test::Mockify->new('SampleLogger', []);
    $MockObjectBuilder->mock('log')->when(String())->thenReturnUndef();
    my $MockedLogger = $MockLoggerBuilder->getMockObject();

    # inject mocked object into the code you want to test
    my $App = SampleApp->new('logger'=> $MockedLogger);
    $App->do_something();

    # verify that the mock object was called
    ok(WasCalled($MockedLogger, 'log'), 'log was called');
    done_testing();

# DESCRIPTION

Use [Test::Mockify](https://metacpan.org/pod/Test::Mockify) to create and configure mock objects. Use [Test::Mockify::Verify](https://metacpan.org/pod/Test::Mockify::Verify) to
verify the interactions with your mocks.

# METHODS

## new

    my $MockObjectBuilder = Test::Mockify->new('Module::To::Mock', ['Constructor Parameters']);

### Options

The `new` method creates a new mock object builder. Use `getMockObject` to obtain the final mock object.

## getMockObject

Provides the actual mock object, which you can use in the test.

    my $aParameterList = ['SomeValueForConstructor'];
    my $MockObjectBuilder = Test::Mockify->new( 'My::Module', $aParameterList );
    my $MyModuleObject = $MockObjectBuilder->getMockObject();

## mock

This is place where the mocked methods are defined. The method also proves that the method you like to mock actually exists.

### synopsis

This method takes one parameter, which is the name of the method you like to mock.
Because you need to specify more detailed the behaviour of this mock you have to chain the method signature (when) and the expected return value (then...). 

For example, the next line will create a mocked version of the method log, but only if this method is called with any string and the number 123. In this case it will return the String 'Hello World'. Mockify will throw an error if this method is called somehow else.

`$MockObjectBuilder->mock('log')->when(String(), Number(123))->thenReturn('Hello World');`

`is($SampleLogger->log('abc',123), 'Hello World');`

### when

To define the signatur in the needed structure you should use the [Test::Mockify::Matchers](https://metacpan.org/pod/Test::Mockify::Matchers).

### whenAny

If you don't want to specify the method signatur at all, you can use whenAny.
It is not possible to mix `whenAny` and `when` for the same method.

### then ...

For possible return types please look in [Test::Mockify::ReturnValue](https://metacpan.org/pod/Test::Mockify::ReturnValue)

## addMethodSpy

With this method it is possible to observe a method. That means, you keep the original functionality but you can get meta data from the mockify-framework.

    $MockObjectBuilder->addMethodSpy('myMethodName');

## addMethodSpyWithParameterCheck

With this method it is possible to observe a method and check the parameters. That means, you keep the original functionality, but you can get meta data from the mockify- framework and use the parameter check, like \*addMockWithReturnValueAndParameterCheck\*.

    my $aParameterTypes = [String(),String(abcd)];
    $MockObjectBuilder->addMethodSpyWithParameterCheck('myMethodName', $aParameterTypes);

To define in a nice way the signatur you should use the [Test::Mockify::Matchers;](https://metacpan.org/pod/Test::Mockify::Matchers;).

## addMock

This is the simplest case. It works like the mock-method from Test::MockObject.

Only handover the \*\*name\*\* and a \*\*method pointer\*\*. Mockify will automatically check if the method exists in the original object.

    $MockObjectBuilder->addMock('myMethodName', sub {
                                      # Your implementation
                                   }
    );

## addMockWithReturnValue

Does the same as `addMock`, but here you can handover a \*\*value\*\* which will be returned if you call the mocked method.

    $MockObjectBuilder->addMockWithReturnValue('myMethodName','the return value');

## addMockWithReturnValueAndParameterCheck

This method is an extension of \*addMockWithReturnValue\*. Here you can also check the parameters which will be passed.

You can check if they have a specific \*\*data type\*\* or even check if they have a given \*\*value\*\*.

In the following example two strings will be expected, and the second one has to have the value "abcd".

    my $aParameterTypes = [String(),String('abcd')];
    $MockObjectBuilder->addMockWithReturnValueAndParameterCheck('myMethodName','the return value',$aParameterTypes);

To define in a nice way the signatur you should use the [Test::Mockify::Matchers;](https://metacpan.org/pod/Test::Mockify::Matchers;).

# LICENSE

Copyright (C) 2017 ePages GmbH

This library is free software; you can redistribute it and/or modify
it under the same terms as Perl itself.

# AUTHOR

Christian Breitkreutz <cbreitkreutz@epages.com>
