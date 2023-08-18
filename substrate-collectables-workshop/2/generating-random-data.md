Generating Random Data
===

At the end of the last section, we allowed each user to create their own kitty. However, they weren't very unique... Let's fix that.

## Generating a Random Seed
If we want to be able to tell these kitties apart, we need to start giving them unique properties! For our app, we need to generate a unique `id` for each kitty and some random `dna`.

We can securely fetch some randomness from our chain using the `system` module:

```rust
<system::Module<T>>::random_seed()
```

Substrate uses a safe mixing algorithm that uses the entropy of previous blocks to generate new random data for each subsequent block.

However, since it is dependent on previous blocks, it can take over 80 blocks to fully warm up, and you may notice the seed will not change until then.

### Using a Nonce

Since the random seed does not change for multiple transactions in the same block, and since it may not even generate a random seed for the first 80 blocks, it is important that we also introduce a `nonce` which our module can manage. Furthermore, we can also use a user specific property like the `AccountId` to introduce a bit more entropy.

If we are able to hash this combined data, we should get enough randomness for our needs.

## Hashing Data

A random number generator on substrate will look something like this:

```rust
let sender = ensure_signed(origin)?;
let nonce = <Nonce<T>>::get();
let random_seed = <system::Module<T>>::random_seed();

let random_hash = (random_seed, sender, nonce).using_encoded(<T as system::Trait>::Hashing::hash);

<Nonce<T>>::mutate(|n| *n += 1);
```

`Nonce` will be a new item in our storage which we will simply increment whenever we use it.

We can use this `random_hash` to populate both the `id` and `dna` for our kitty.

## Checking for Collision

To easily track all of our kitties, it would be helpful to standardize our logic to use a unique id as the global key for our storage items. This means that a single unique key will point to our `Kitty` object, and all other ownership links or maps will point to that key.

The `id` on the `Kitty` object will serve that purpose, but we need to make sure that the `id` for a new kitty is always unique. We can do this with a new storage item `Kitties` which will be a mapping from `id` (`Hash`) to the `Kitty` object.

With this object, we can easily check for collisions by simply checking whether this storage item already contains a mapping using a particular `id`. Something like:

```rust
ensure!(!<Kitties<T>>::exists(new_id), "This new id already exists");
```

However unlikely it is that two randomly generated hashes may collide, it is important to make this check as we may introduce other ways to generate kitties, and our storage structure depends on this uniqueness.

## Your Turn!

Let's update our module to support generating random data for our kitties, and use that random data to create unique `id`s which will be the basis for our storage and logic.

We will introduce a new `Kitties` storage items which will map an `id` to a `Kitty` object. We will also want to create a `KittyOwner` storage item which will map an `id` to the `AccountId` who owns the kitty.

Finally we can update our `OwnedKitty` object to point to this unique `id` rather than have a duplicate copy of the `Kitty` object in our storage.

Now that we have set up all these new storage items, we will need to also update our `create_kitty()` function to correctly update these storage items when a kitty is made.

Follow the instructions on the template for this section to get things running!

<!-- tabs:start -->

#### ** Template **

[embedded-code](./assets/2.1-template.rs ':include :type=code embed-template')

#### ** Solution **

[embedded-code-final](./assets/2.1-finished-code.rs ':include :type=code embed-final')

#### ** Previous Chapter Solution **

[embedded-code-previous](../1/assets/1.6-finished-code.rs ':include :type=code embed-previous')

<!-- tabs:end -->