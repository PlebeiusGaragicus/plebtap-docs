# plebtap-docs

These docs are for [PlebTap](https://github.com/PlebeiusGaragicus/plebtap), which brings [Nostr](https://github.com/nostr-protocol/nips), [Lightning](https://github.com/lightning/blips) and [Ecash](https://github.com/cashubtc/nuts) all together in a single Svelte 5 Button component.

Just drop the component into your app:

```svelte
<script lang="ts">
  import { plebtap } from 'plebtap';
</script>

<Plebtap />
```

That's it! The button handles everything - login, wallet management, sending/receiving payments.