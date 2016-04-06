---
title: Step notifications
layout: gem-single
---

As well as matching on the final transaction result, you can subscribe to individual steps and trigger specific behaviours based on their success or failure:

```ruby
NOTIFICATIONS = []

save_user = Dry.Transaction(container: Container) do
  step :process
  step :validate
  step :persist
end

module UserPersistListener
  extend self

  def persist_success(user)
    NOTIFICATIONS << "#{user[:email]} persisted"
  end

  def persist_failure(user)
    NOTIFICATIONS << "#{user[:email]} failed to persist"
  end
end

input = {"name" => "Jane", "email" => "jane@doe.com"}

save_user.subscribe(persist: UserPersistListener)
save_user.call(input, validate: ["doe.com"])

NOTIFICATIONS
# => ["jane@doe.com persisted"]
```

This pub/sub mechanism is provided by the [Wisper](https://github.com/krisleech/wisper) gem. You can subscribe to specific steps using the `#subscribe(step_name: listener)` API, or subscribe to all steps via `#subscribe(listener)`.