# emq_elixir_plugin

This is an EMQ plugin template that enables you to write EMQ plugins using Elixir.

Here is an example, supposing you are interested in using a publish-message hook:

## Step 1
Clone the `emq-relx` project and its dependencies.
```
$ git clone https://github.com/emqtt/emq-relx.git
$ cd emq-relx
$ make
```
emq-relx/deps/emq_elixir_plugin is the working directory of following steps.


## Step 2
This step will load `emq-elixir-plugin` into the `emq-relx` project. `emq-elixir-plugin` is not included in the release project by default, since it is just a development template.

+ Modify `emq-relx/Makefile`:
  - Append `emq_elixir_plugin` to the `DEPS` variable
  - Uncomment the following line:
```
dep_emq_elixir_plugin = git https://github.com/emqtt/emq-elixir-plugin master
```
+ Modify `relx.conf`
  - Uncomment the following line, below '{emq_lua_hook, load}:'
```
{emq_elixir_plugin, load},
```
+ Re-make, in order to install the new dependencies (inside `emq-relx`'s root):
```
$ make
```

## Step 3
Uncomment the following line in the `load/1` function of `deps/emq_elixir_plugin/lib/emq_elixir_plugin
/elixir_plugin_body.ex`
```elixir
hook_add(:"message.publish",      &EmqElixirPlugin.Body.on_message_publish/2,     [env])
```
and uncomment the following line in `unload/0` of the same file:
```elixir
hook_del(:"message.publish",      &EmqElixirPlugin.Body.on_message_publish/2     )
```

## Step 4
Write your code inside the `on_message_publish/2` function:
```elixir
def on_message_publish(message, _env) do
    IO.inspect(["elixir on_message_publish", message])
        
    # add your elixir code here
        
    {:ok, message}
end
```

## Step 5
Compile your code (again, inside `emq-relx`'s root):
```
$ make
```

## Step 6
Run your code
```
$ cd emq-relx/_rel/emqttd
$ bin/emqttd start
$ bin/emqttd_ctl plugins load emq_elixir_plugin
```
Your elixir plugin should be working now.



