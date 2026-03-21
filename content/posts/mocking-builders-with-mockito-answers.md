---
author: splatch
category:
  - uncategorized
date: "2020-04-29T11:58:55+00:00"
guid: http://dywicki.pl/?p=952
summary: I've been looking for some ways to use Mockito to with builder pattern using fluent calls. There are couple of articles online, however none of these addressed my case, a builder which receives calls and returns fully working object after a final ''build'' call without the need for further setup.
tag:
  - mockito
  - unit-tests
title: Mocking builders with Mockito answers
url: /2020/04/mocking-builders-with-mockito-answers/

---
I've been looking for some ways to use Mockito to with builder pattern using fluent calls. There are couple of articles online, however none of these addressed my case, a builder which receives calls and returns fully working object after a final ''build'' call without the need for further setup.

###### TL;DR

You can combine multiple mocks using Mockito's answer implementations which mocks getter calls on a second mock instance.

## Problem overview

You might ask why I would care about that, well - in my case a Builder is an interface and built thing is also an interface resulting in situation that I have to mock two instances in order to make test pass. First - the builder to keep returning itself, second an object which needs to be created at the end.  
In this short post I will explain how I did it. You might find this code useful especially if you do have a lot of mocking of builders and their return values and look for a way to unify that part.

That's the order of actions we gonna do:

- Create a instance of mock builder - in my case `BridgeBuilder`.
- Create instance of type to be created by builder - a `Bridge` in below code.
- For each call on `BridgeBuilder` we will mock a getter call on `Bridge` with return value set via builder.

I come to following shape of code which covers the above flow. That's how the mocking preparation looks a like:

```
BridgeBuilder bridgeBuilder = mock(BridgeBuilder.class, "bridgeBuilderMock");
Bridge bridge = mock(Bridge.class, "bridgeMock");
builder(
  bridgeBuilder,
  () -> bridgeBuilder.withConfiguration(any()),
  bridge::getConfiguration,
  new MappedReturnValue<>()
);
builder(bridgeBuilder, () -> bridgeBuilder.withBridge(any()), bridge::getBridgeUID, new MappedReturnValue<>());
builder(bridgeBuilder, () -> bridgeBuilder.withLabel(any()), bridge::getLabel, new MappedReturnValue<>());
builder(bridgeBuilder, () -> bridgeBuilder.withLocation(any()), bridge::getLocation, new MappedReturnValue<>());
builder(bridgeBuilder, () -> bridgeBuilder.withChannels(anyList()), bridge::getChannels, new MappedReturnValue<>());
when(bridgeBuilder.build()).thenReturn(bridge);
```

As you can see it is relatively simple. It is a short notation to say that we return a builder (line 4). Additionally for each call on this mock (line 5) we will setup a getter call (line 6) using a MappedReturnValue function (line 7). Lets take a look on `builder` method.

```
private <T, X> void builder(T mock, Supplier<T> builderCall, Supplier<X> getterCall, Function<InvocationOnMock, X> returnValue) {
    MapBuilderToMock<T, X> answer = new MapBuilderToMock<>(mock, getterCall, returnValue);
    when(builderCall.get()).thenAnswer(answer);
}
```

That's a generic block which you would normally see - with one difference there is no "mock" returned but an answer. I've decided also to use a separate class instead of anonymous/lambda in order to increase readiness.  
If you come to this article you probably know the syntax of `when` and `thenAnswer`, which is used above.  
I will just note here that answer implementation is superior to `thenReturn` cause it will have access to arguments passed to builder method call. With such powerful tool we can execute additional logic, and that's what we really need. More importantly as you can see, you can record calls in lambda. It is possible because `Supplier` can be seen as portion of code which should be executed each time when its `get()` method is executed. For Mockito it doesn't matter how we call code, as long as it is able to record method invocations. That's why tool doesn't complain with `when(builderCall.get())`.

Lets take a look on the `MapBuilderToMock` answer:

```
static class MapBuilderToMock<T, X> implements Answer<T> {
    private final T builder;
    private final Supplier<X> getter;
    private final Function<InvocationOnMock, X> returnValue;

    public MapBuilderToMock(T builder, Supplier<X> getter, Function<InvocationOnMock, X> returnValue) {
        this.builder = builder;
        this.getter = getter;
        this.returnValue = returnValue;
    }
    @Override
    public T answer(InvocationOnMock invocation) throws Throwable {
        when(getter.get()).thenReturn(returnValue.apply(invocation));
        return builder;
    }
}

```

What is really important is line 13, where we have another lambda call which transforms builder invocation to return value of a lambda. Very short, initial approach can be achieved by this:

```
when(getter.get()).thenReturn(invocation.getArgument(0));
```

### Working code

After explanation - we can see whole code. Additional classes makes it longer than necessary, however my intention here is keeping these blocks separate in order to increase readability.

```
@Test
public void testDifferentHandlerFactoriesForBridgeAndThing() {
    BridgeBuilder bridgeBuilder = mock(BridgeBuilder.class, "bridgeBuilder");
    Bridge bridge = mock(Bridge.class, "bridge");
    builder(bridgeBuilder, () -> bridgeBuilder.withConfiguration(any()), bridge::getConfiguration, new MapArgument<>());
    builder(bridgeBuilder, () -> bridgeBuilder.withBridge(any()), bridge::getBridgeUID, new MapArgument<>());
    builder(bridgeBuilder, () -> bridgeBuilder.withLabel(any()), bridge::getLabel, new MapArgument<>());
    builder(bridgeBuilder, () -> bridgeBuilder.withLocation(any()), bridge::getLocation, new MapArgument<>());
    builder(bridgeBuilder, () -> bridgeBuilder.withChannels(anyList()), bridge::getChannels, new MapArgument<>());
    when(bridgeBuilder.build()).thenReturn(bridge);

    Thing thing = mock(Thing.class, "thing");
    ThingBuilder thingBuilder = mock(ThingBuilder.class, "thingBuilder");
    builder(thingBuilder, () -> thingBuilder.withConfiguration(any()), thing::getConfiguration, new MapArgument<>());
    builder(thingBuilder, () -> thingBuilder.withBridge(any()), thing::getBridgeUID, new MapArgument<>());
    builder(thingBuilder, () -> thingBuilder.withLabel(any()), thing::getLabel, new MapArgument<>());
    builder(thingBuilder, () -> thingBuilder.withLocation(any()), thing::getLocation, new MapArgument<>());
    builder(thingBuilder, () -> thingBuilder.withChannels(anyList()), thing::getChannels, new MapArgument<>());
    when(thingBuilder.build()).thenReturn(thing);

    // rest of the test
}

private <T, X> void builder(T mock, Supplier<T> builderCall, Supplier<X> getterCall, Function<InvocationOnMock, X> returnValue) {
    MapBuilderToMock<T, X> answer = new MapBuilderToMock<>(mock, getterCall, returnValue);
    when(builderCall.get()).thenAnswer(answer);
}

static class MappedReturnValue<X> implements Function<InvocationOnMock, X> {
    private final int index;

    public MappedReturnValue() {
        this(0);
    }

    public MappedReturnValue(int index) {
        this.index = index;
    }
    @Override
    public X apply(InvocationOnMock invocationOnMock) {
        return invocationOnMock.getArgument(index);
    }
}

static class MapBuilderToMock<T, X> implements Answer<T> {
    private final T builder;
    private final Supplier<X> getter;
    private final Function<InvocationOnMock, X> returnValue;

    public MapBuilderToMock(T builder, Supplier<X> getter, Function<InvocationOnMock, X> returnValue) {
        this.builder = builder;
        this.getter = getter;
        this.returnValue = returnValue;
    }
    @Override
    public T answer(InvocationOnMock invocation) throws Throwable {
        when(getter.get()).thenReturn(returnValue.apply(invocation));
        return builder;
    }
}
```

### Summary

Above code allows you to build fully functional mocks for builders and their return values in a portable way. Thanks to possibility to record mock calls via lambda arguments we can make preparation process fast and easy to read. Use of additional logic is necessary only to map arguments received by builder to instance it is supposed to return at the end.
