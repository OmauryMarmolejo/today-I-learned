# Prefer Noun-Adjective Naming

From the post: [Prefer Noun-Adjective Naming](https://kyleshevlin.com/prefer-noun-adjective-naming/?utm_source=tldrwebdev)

In English grammar, adjectives comes before the noun: The red car, the big house, the old man, etc.

In programming, we often name things in the same way: `PrimaryButton`, `DefaultHeader`, `StandardLayout`, etc.

However, there are times when it makes more sense to name things with the noun first. One of the reasons is that it helps to have our things in an alphabetical order.

For example, `Stream`. A `Stream` can be `created`, `ended`, and `live`.

If we follow English grammar and make components for each state, we end up with `CreatedStream`, `EndedStream`, and `LiveStream`, etc.

```
- CreatedStream.tsx
- CurrentUser.tsx
- EndedStream.tsx
- FullScreen.tsx
- GoLive.tsx
- LiveStream.tsx
```

But if we name them with the noun first, we could have something like `StreamCreated, StreamEnded`, and `StreamLive`, all the components are right next to each other in your directory.

```
- CurrentUser.tsx
- FullScreen.tsx
- GoLive.tsx
- StreamCreated.tsx
- StreamEnded.tsx
- StreamLive.tsx
```

This makes it easier to find related components and keep your codebase organized.
