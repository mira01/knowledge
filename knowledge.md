Live upgrades
=============

appup and relup files are generated for automatic live upgrade

There have to be two tags in git and the app.src has to contain ```{vsn, git}```.
$BASE and $TARGET versions are upgrade vector.

how to generate:

``` shell
git reset --hard $BASE
rebar3 release
rebar3 sappan checkrel

git reset --hard $TARGET
rebar3 release
rebar3 appup generate -g default=brutal/soft
rebar3 sappan generate_config
rebar3 sappan checkrel
rebar3 sappan check_upgrade
rebar3 relup
rebar3 tar   
```

Then open relup file and check instructions; If needed, use following code to reorder instuctions.
In this example instruction ```{apply, {...}}``` will be run directly after ```{add_module}```

``` erlang rebar.config
{sappan, [
    {post_instrs_upgrade, [
        {{add_module, cfg_storage, []}, [
            {apply,{cfg_storage,init_backends_table, []}}
        ]}
    ]}
]}.

```
