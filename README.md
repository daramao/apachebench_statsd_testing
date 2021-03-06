### Level 1

* <strong>Sign up for Datadog, get the agent reporting metrics from your local machine.<br>Answer:</strong> I have set up the agent reporting metrics for my local machine: `host:Daras-MacBook-Air-2.local`

* <strong>Bonus question: what is the agent? <br> Answer: </strong> The agent is a piece of software that collects events and metrics on the users hosts for monitoring and managing performance data.

* <strong>Submit an event via the API.<br> Answer: </strong>
 To submit an event via the API, my code:

     require 'dogapi'
     api_key = 'DATADOG_API_KEY'
     dog = Dogapi::Client.new(api_key)

     response = dog.emit_event(Dogapi::Event.new('Level 1 question 3', :msg_title => 'Support Engineer Dara Mao'))


* <strong>Get an event to appear in your email inbox (the email address you signed up for the account with)<br> Answer: </strong> By adding @dara.mao@gmail.com in the event body.
 My code:

     require 'dogapi'
     api_key = 'DATADOG_API_KEY'
     dog = Dogapi::Client.new(api_key)

     response = dog.emit_event(Dogapi::Event.new('Level 1 question 4 to @dara.mao@gmail.com', :msg_title => 'Support Engineer Dara Mao'))


### Level 2

* <strong>Take a simple web app ([in any of our supported languages](http://docs.datadoghq.com/libraries/)) that you've already built and instrument your code with dogstatsd. This will create **metrics**.<br> Answer: </strong> I have implemented dogstatsd for one of my rails project, [Blackbox](https://github.com/daramao/blackbox2014).

* <strong>While running a load test (see References) for a few minutes, visualize page views per second. Send us the link to this graph!<br> Answer: </strong> This is the [page view graph](https://app.datadoghq.com/dash/dash/26531?from_ts=1408118740578&to_ts=1408122340578&tile_size=m&fullscreen=50557073) I generated using ApacheBench load test as follows:
![pageview](level2PageView.png)

  My code for page view:

     require 'statsd'
     statsd = Statsd.new
     statsd.increment('page.views')

  My code for load test:

     ab -n 157 -c 10 http://127.0.0.1:3000/periods/show
     ab -n 2000 -c 10 http://127.0.0.1:3000/periods/show
     ab -n 364 -c 100 http://127.0.0.1:3000/periods/show
     ab -n 10034 -c 1 http://127.0.0.1:3000/periods/show
     ab -n 100 -c 10 http://127.0.0.1:3000/periods/show
     ab -n 750 -c 100 http://127.0.0.1:3000/periods/show

* <strong>Create a histogram to see the latency; also give us the link to the graph. Bonus points for putting together more creative dashboards.<br> Answer: </strong> This [dashboard](https://app.datadoghq.com/dash/dash/26531?from_ts=1408119105197&to_ts=1408127463053&tile_size=m) includes histogram along with page view load test.

![latency](latency.png)

  My code for histogram:

     require 'statsd'
     statsd = Statsd.new
     begin = Time.now
     statsd.increment('page.views')
     end = Time.now
     frame = end - begin
     statsd.histogram('statsd.increment.latency', frame)



### Level 3

* <strong>tag your metrics with `support` (one tag for all metrics)</strong>
* <strong>tag your metrics per page.<br> Answer: </strong> I have added `:tags =>['support, support:page1']` and `:tags =>['support, support:page2']` to all the metrics for 2 pages of the app.
* <strong>visualize the latency by page on a graph (using stacked areas, with one color per `page`)<br> Answer: </strong> Attached [dashboard](https://app.datadoghq.com/dash/dash/26557?from_ts=1408130627940&to_ts=1408131066693&tile_size=m) of graphs.

![tags](level3tags.png)

My code for load test:

     ab -n 120 -c 100 http://127.0.0.1:3000/periods
     ab -n 500 -c 10 http://127.0.0.1:3000/periods/show
     ab -n 45 -c 1 http://127.0.0.1:3000/periods
     ab -n 1532 -c 10 http://127.0.0.1:3000/periods/show
     ab -n 3495 -c 1 http://127.0.0.1:3000/periods
     ab -n 1200 -c 100 http://127.0.0.1:3000/periods/show

### Level 4

* <strong>count the overall number of page views using dogstatsd counters.</strong>
* <strong>count the number of page views, split by page (hint: use tags)</strong>
* <strong>visualize the results on a graph</strong>
* <strong>Bonus question: do you know why the graphs are very spiky?<br> Answer: </strong> Attached [dashboard](https://app.datadoghq.com/dash/dash/26561?from_ts=1408130545001&to_ts=1408131288068&tile_size=m) of graphs. The spikes in the graphs are due to time lapse in page load.

![counter](level4.png)



### Level 5

* <strong>Write an agent check that samples a random value. Call this new metric: `test.support.random`<br> Answer: </strong>
My code for random_test.py:

     import random
     import statsd

     from checks import AgentCheck
     class RandomCheck(AgentCheck):
      def check(self, instance):
        self.gauge('test.support.random', random.random())
        self.event({
          "api_key": "DATADOG_API_KEY",
          "msg_title": "testing random AgentCheck",
          "tags": "test.support.random"
             })

            print(random.random())

          from statsd import statsd
          statsd.gauge('test.support.random', random.random())

  My code for random_test.yaml:

     init_config:
      default_timeout: 5

     instances:
      -   url: http://127.0.0.1:3000/periods


* <strong>Visualize this new metric on Datadog, send us the link.<br> Answer: </strong>I ran a few random tests, see attached [dashboard](https://app.datadoghq.com/dash/dash/26611?from_ts=1408221072790&to_ts=1408221687265&tile_size=m) of graphs.

![agentCheck](level5.png)
