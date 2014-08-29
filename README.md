N2O: Erlang Web Framework
=========================

Features
--------

* Formatters: BERT, JSON, Binary or Custom
* Protocols: Nitrogen, RoR, N2O
* Endpoints: WebSocket, HTTP, REST
* PubSub: MQS, GPROC
* Templates: DTL
* DOM Language: Nitrogen DSL, Erlang Shen
* Speed: 15K req/s
* Security: AES CBC 128

Sample Page in DSL
------------------

```erlang
-module(index).
-compile(export_all).
-include_lib("n2o/include/wf.hrl").

peer()    -> io_lib:format("~p",[wf:peer(?REQ)]).
message() -> wf:js_escape(wf:html_encode(wf:q(message))).
main()    -> #dtl{file="index",app=n2o_sample,bindings=[{body,body()}]}.
body() ->
    {ok,Pid} = wf:comet(fun() -> chat_loop() end),
    [ #panel{id=history}, #textbox{id=message},
      #button{id=send,body="Chat",postback={chat,Pid},source=[message]} ].

event(init) -> wf:reg(room);
event({chat,Pid}) -> Pid ! {peer(), message()};
event(Event) -> skip.

chat_loop() ->
    receive {Peer, Message} ->
       wf:insert_bottom(history,#panel{id=history,body=[Peer,": ",Message,#br{}]}),
       wf:flush(room) end, chat_loop().
```

And try to compare how this functionality would be implemented
with your favourite language/framework.

Performance
-----------

We are using for measurement ab, httperf, wrk and siege, all of them. The most valuable storm
created by wrk and it is not achieved in real apps but could show us the internal throughput
of individual components. The most near to real life apps is siege who also make DNS lookup
for each request. So this data shows internal data throughput by wrk:

| Framework | Enabled Components | Speed | Timeouts |
|-----------|--------------------|-------|----------|
| PHP5 FCGI | Simple script with two <?php print "OK"; ?> terms inside | 5K | timeouts |
| Nitrogen  | No sessions, No DSL, Simple template with two variable | 1K | no |
| N2O       | All enabled, sessions, Template, heavy DSL | 7K | no |
| N2O       | Sessions enabled, template with two variables, no DSL | 10K | no |
| N2O       | No sessions, No DSL, only template with two vars | 15K | no |

Kickstart Bootstrap
-------------------

To try N2O you just need to clone a N2O repo from Github and build.
We use very small and powerfull mad tool designed for our Web Stack.

    $ cd n2o/samples
    $ ./mad deps compile plan repl

Now you can try: [http://localhost:8000](http://localhost:8000)

LINUX NOTE: if you want to have online recompilation you should do at first:

    $ sudo apt-get install inotify-tools

Support
-------

* IRC Channel #n2o on FreeNode 24/7
* Official N2O Book [HTML](http://synrc.com/framework/web/) and [PDF](https://synrc.com/apps/n2o/doc/book.pdf)

Credits
-------

* Maxim Sokhatsky -- core, shen, windows
* Dmitry Bushmelev -- endpoints, yaws, cowboy
* Andrii Zadorozhnii -- elements, actions, handlers
* Anton Logvinenko -- doc
* Vladimir Kirillov -- mac, bsd, xen, linux support
* Roman Shestakov -- advanced elements, ct
* Jesse Gumm -- nitrogen, help
* Rusty Klophaus -- original author

OM A HUM
