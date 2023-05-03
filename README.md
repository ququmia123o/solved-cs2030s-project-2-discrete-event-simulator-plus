Download Link: https://assignmentchef.com/product/solved-cs2030s-project-2-discrete-event-simulator-plus
<br>
<h4>Requirements</h4>

<ul>

 <li>Immutability</li>

 <li>Pure Functions</li>

 <li>Randomizing the arrival and service times</li>

 <li>Object-oriented design with possibly a mix of imperative/declarative (functional) implementations</li>

 <li>Program style: adherance to <a href="https://www.comp.nus.edu.sg/~cs2030/style/" target="_blank" rel="noopener">CS2030 Java Style Guide</a></li>

 <li>Java documentation: adherence to <a href="https://www.comp.nus.edu.sg/~cs2030/javadoc" target="_blank" rel="noopener">CS2030 Javadoc Specification</a></li>

</ul>

<h4>Problem Description</h4>

This stage of the project comprises three parts:

<ul>

 <li>Dealing with side-effects</li>

 <li>Randomizing arrival and service times</li>

 <li>More complex behavior of shops, servers, and customers</li>

</ul>

<h4>Dealing with Side-Effects</h4>

For this part of the project, we shall first tackle the issue of <i>side effects</i> in <a href="https://codes.comp.nus.edu.sg/task_view.php?tid=4948">Project #1</a>. You would probably have coded something similar to the following, when processing the different <tt>Event</tt>s (arrive, serve, wait, leave, done, …) in the <tt>PriorityQueue pq</tt>.

<pre>while (!pq.isEmpty()) {	Event e;	e = pq.poll();	System.out.println(e);	Event nextEvent = e.execute();	if (nextEvent.isValidEvent()) {		pq.add(nextEvent);	} }</pre>

Notice that every time the <tt>execute</tt> method of an event is invoked, it not only generates the next event based on the status of the <tt>Servers</tt> (here we call it a <tt>Shop</tt>), it will actually change the status of the servers as a side-effect. As such, we would like to address this side-effect by re-defining event generation as follows:

<pre>e.execute(s) -&gt; (e',s') </pre>

That is to say, the invocation of the <tt>execute</tt> method of an <tt>Event e</tt> with the argument <tt>Shop s</tt>, would now generate the next <tt>Event e'</tt> and <tt>Shop s'</tt> as a pair of values. In a similar fashion, <tt>e'</tt>and <tt>s'</tt> can then be used to generate the next pair of values.

<h4>Randomized Arrival and Service Time</h4>

Rather than fixed arrival and service times, we will generate random times. A random number generator is an entity that generates one random number after another. Since it is not possible to generate a truly random number algorithmically, pseudo random number generation is adopted instead. A pseudo-random number generator can be initialized with a seed, such that the same seed always produces the same sequence of (seemingly random) numbers.

Although, Java provides a class <tt>java.util.Random</tt>, an alternative <tt>RandomGenerator</tt> class that is more suitable for discrete event simulation is provided for you that encapsulates different random number generators for use in our simulator. Each random number generator generates a different stream of random numbers. The constructor for <tt>RandomGenerator</tt> takes in three parameters:

<ul>

 <li><tt>int seed</tt> is the base seed. Each random number generator uses its own seed that is derived from this base seed;</li>

 <li><tt>double lambda</tt> is the arrival rate, λ;</li>

 <li><tt>double mu</tt> is the service rate, μ.</li>

</ul>

The <strong>inter-arrival time</strong> is usually modeled as an exponential random variable, characterized by a single parameter <tt>λ</tt> denoting the arrival rate. The <tt>genInterArrivalTime()</tt> method of the class <tt>RandomGenerator</tt> is used for this purpose. Specifically,

<ul>

 <li>start the simulation by generating the first customer arrival event with timestamp <tt>0</tt></li>

 <li>if there are still more customers to simulate, generate the next arrival event with a timestamp of <tt>T + now</tt>, where <tt>T</tt> is generated with the method <tt>genInterArrivalTime()</tt>;</li>

</ul>

The <strong>service time</strong> is modeled as an exponential random variable, characterized by a single parameter, service rate μ. The method <tt>genServiceTime()</tt> from the class <tt>RandomGenerator</tt> can be used to generate the service time. Specifically,

<ul>

 <li>each time a customer is being served, a <tt>DONE</tt> event is generated and scheduled;</li>

 <li>the <tt>DONE</tt> event generated will have a timestamp of <tt>T + now</tt>, where <tt>T</tt> is generated with the method <tt>genServiceTime()</tt>.</li>

</ul>

You may refer to the API of the <tt>RandomGenerator</tt> class <a href="http://www.comp.nus.edu.sg/~cs2030/RandomGenerator">here</a>. The class file can be downloaded from <a href="http://www.comp.nus.edu.sg/~cs2030/RandomGenerator/RandomGenerator.class">here</a>.

Note that <tt>RandomGenerator</tt> class resides in the <tt>cs2030.simulator</tt> package. So, <tt>RandomGenerator.class</tt> should be saved in the <tt>cs2030/simulator</tt> directory.

<h4>More Complex Behaviour of Shops, Serves and Customers</h4>

You will extend the simulator to model the following entities.

<ul>

 <li>FIFO (first-in-first-out) queues for customers with a given maximum capacity</li>

 <li>Two new events

  <ul>

   <li><tt>SERVER_REST</tt> that simulates a server taking a rest, and</li>

   <li><tt>SERVER_BACK</tt> that simulates a server returning back from rest</li>

  </ul></li>

 <li>Two types of servers,

  <ul>

   <li><em>human servers</em> who may rest after serving a customer, and</li>

   <li><em>self-checkout counters</em> that never rest</li>

  </ul></li>

 <li>Two types of customers

  <ul>

   <li><em>typical</em> customers that joins the first queue (scanning from server 1 onwards) that is still not full, and</li>

   <li><em>greedy</em> customers that joins the queue with the fewest waiting customers</li>

  </ul></li>

</ul>

<h4>Customer Queues</h4>

Each human server now has a queue of customers to allow multiple customers to queue up. A customer that chooses to join a queue joins at the tail. When a server is done serving a customer, it serves the next waiting customer at the head of the queue. Hence, the queue should be a first-in-first-out (FIFO) structure. <strong>The self-checkout counters, however, have only a single shared queue.</strong>

<h4>Taking a Rest</h4>

The human servers are allowed to take occasional breaks. When a server finishes serving a customer, there is a probability <tt>P<sub>r</sub></tt> that the server takes a rest for a random amount of time <tt>T<sub>r</sub></tt>. During the break, the server does not serve the next waiting customer. Upon returning from the break, the server serves the next customer in the queue immediately.

To implement this behavior, introduce two new events, <tt>SERVER_REST</tt> and <tt>SERVER_BACK</tt>, to simulate taking a break, and returning. These events should be generated and scheduled in the simulator when the server decides to rest.

<h4>Self-Checkout</h4>

To reduce waiting time, self-checkout counters have been set-up. These self-checkout counters never need to rest. Customers queue up for the self-checkout counters in the same way as <em>human</em> servers. There is one shared queue for all self-checkout counters.

<h4>Customers’ Choice of Queue</h4>

As before, when a customer arrives, he or she first scans through the servers (in order, from <tt>1</tt> to <tt>k</tt>) to see if there is an idle server (i.e. not serving a customer and not resting). If there is one, the customer will go to the server to be served. Otherwise, a typical customer just chooses the first queue (while scanning from servers <tt>1</tt> to <tt>k</tt>) that is still not full to join. However, other than the typical customer, a <em>greedy</em> customer is introduced that always joins the queue with the fewest customers. In the case of a tie, the customer breaks the tie by choosing the first one while scanning from servers <tt>1</tt> to <tt>k</tt>.

If a customer cannot find any queue to join, he/she will leave the shop.

All classes dealing with the simulation should now reside in the <tt>cs2030.simulator</tt> package, with the <tt>Main</tt> class <b>outside</b> the package, but importing the necessary classes from the package.

<h4>Program Style</h4>

Check for styling errors by invoking <tt>checkstyle</tt>. For example, to check styling for all java files

<pre>$ java -jar checkstyle-8.2-all.jar -c cs2030_checks.xml *.java</pre>

<h4>Writing and Generating Javadoc</h4>

You are to document your classes and methods with Javadoc comments. For more details, see the <a href="http://www.comp.nus.edu.sg/~cs2030/javadoc">javadoc</a> guide.

<h4>The Task</h4>

This task is divided into several levels. As the levels get progressively more complex, specific details will be provided in the corresponding levels.

As usual, you will need to keep track of the following statistics:

<ol>

 <li>the average waiting time for customers who have been served</li>

 <li>the number of customers served</li>

 <li>the number of customers who left without being served</li>

</ol>

Take note of the following assumptions:

<ul>

 <li><strong>There is no longer an upper bound for the number of customers</strong>;</li>

 <li>The format of the input is always correct;</li>

 <li>Output of a <tt>double</tt> value, say <tt>d</tt>, is to be formatted with <tt>String.format("%.3f", d)</tt>;</li>

</ul>

<b>You have to complete ALL levels.</b>




<table border="1" cellpadding="10">

 <tbody>

  <tr>

   <td><h4>Level 1</h4><b>Levels 1 and 2 are specified with very rigid requirements, primarily so that students can meet the desired learning outcomes for the project.</b> Do not worry if your code is packaged in <tt>cs2030.simulator</tt>. CodeCrunch will ignore the packages when testing in JShell.First, we shall implement an <i>immutable</i> <tt>Shop</tt> class to hold the <tt>Servers</tt>. Recall that our <tt>Server</tt> class is defined as follows:<pre>class Server {    ...    Server(int identifier, boolean isAvailable, boolean hasWaitingCustomer, double nextAvailableTime) {        ...    }}</pre>The <tt>Shop</tt> class is to be developed using a very <i>functional</i> style. Hence <b>no loops are allowed</b>. Do not write explicit for-loops or while-loops or recursion; you may use implicit loops such as Java streams.<pre><b>jshell&gt; new Shop(2)</b>$.. ==&gt; [1 is available, 2 is available]<b>jshell&gt; Shop shops = new Shop(List.of(new Server(1, true, false, 0), new Server(2, false, false, 1.0)))</b><b>jshell&gt; shops</b>shops ==&gt; [1 is available, 2 is busy; available at 1.000]<b>jshell&gt; shops.find(x -&gt; x.isAvailable())</b>$.. ==&gt; Optional[1 is available]<b>jshell&gt; new Shop(2).find(x -&gt; x.isAvailable())</b>$.. ==&gt; Optional[1 is available]<b>jshell&gt; shops.find(x -&gt; x.isAvailable()).ifPresent(System.out::println)</b>1 is available<b>jshell&gt; Server s = new Server(1, false, false, 2.0)</b><b>jshell&gt; shops.replace(s)</b>$.. ==&gt; [1 is busy; available at 2.000, 2 is busy; available at 1.000]<b>jshell&gt; shops.replace(s).find(x -&gt; x.isAvailable())</b>$.. ==&gt; Optional.empty<b>jshell&gt; shops</b>shops ==&gt; [1 is available, 2 is busy; available at 1.000]<b>jshell&gt; /exit</b></pre></td>

  </tr>

  <tr>

   <td><h4>Level 2</h4>Before proceeding to the <tt>Event</tt> class, you will first need to implement a generic <tt>Pair&lt;T,U&gt;</tt> class. This class is useful when returning multiple values from a function.<pre><b>jshell&gt; Pair&lt;Integer,String&gt; pair = Pair.of(1, "one");</b><b>jshell&gt; pair.first()</b>$.. ==&gt; 1<b>jshell&gt; pair.second()</b>$.. ==&gt; "one"<b>jshell&gt; Pair&lt;Long,Long&gt; pair = Pair.of(0L, 100L);</b><b>jshell&gt; pair.first()</b>$.. ==&gt; 0<b>jshell&gt; pair.second()</b>$.. ==&gt; 100<b>jshell&gt; /exit</b></pre>As for the <tt>Event</tt> class, previously we created different events by overridding the <tt>execute</tt> method in Event. Rather than substitution via overriding, we shall use substitution via lambdas!The <tt>Event</tt> class is now required to have a property of type <tt>Function&lt;Shop, Pair&lt;Shop, Event&gt;&gt;</tt>. Different types of events shall assign this lambda to a different functionality. For example, suppose we have a <tt>DummyEvent</tt> defined with a functionality that takes in a <tt>Shop</tt> comprising some <tt>Servers</tt>, and creates an empty shop and another dummy event. So <tt>DummyEvent</tt> will be defined as follows (here we assume that the <tt>Event</tt> has a constructor that only takes a <tt>Function</tt> as argument):<pre>class DummyEvent extends Event {    DummyEvent() {        super(x -&gt; new Pair&lt;Shop,Event&gt;(new Shop(), new DummyEvent()));    }    @Override    public String toString() {        return "DummyEvent";    }}jshell&gt; Event e = new DummyEvent()e ==&gt; DummyEventjshell&gt; e.execute(new Shop(1))$.. ==&gt; <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="baeadbd3c8fa9494949494949494">[email protected]</a>jshell&gt; e.execute(new Shop(1)).first()$.. ==&gt; []jshell&gt; e.execute(new Shop(1)).second()$.. ==&gt; DummyEvent</pre>Notice that the <tt>execute</tt> method of the <tt>Event</tt> class can be defined simply as:<pre>final Pair&lt;Shop, Event&gt; execute(Shop shop) { // declared final to avoid overriding    return this.func.apply(shop); // func is the Function property}</pre>Invoking <tt>execute</tt> with a <tt>Shop</tt> will result in a <tt>Pair</tt> comprising a <tt>Shop</tt> (which is empty), and another <tt>DummyEvent</tt>. As you can see, other than <tt>Event</tt> itself, all specific types of events — arrive, serve, wait, done and leave, can now be fully specified with their relevant properties, and the addition of only one <tt>toString()</tt> method. <b>Note that this constraint shall be enforced during grading.</b> You still have the liberty of constructing your <tt>Event</tt> class in any way you wish.With this, you can now test the behaviour and correctness of your events by passing in a Shop and checking the returned <tt>Pair</tt>. Below is a snippet of how arrival events can be tested.<pre><b>jshell&gt; // one available server with no waiting customer</b><b>jshell&gt; new ArriveEvent(new Customer(1, 1.0)).execute(new Shop(List.of(new Server(1,true,false,0)))).first() // (*) will not be tested</b>$.. ==&gt; [1 is available]<b>jshell&gt; new ArriveEvent(new Customer(1, 1.0)).execute(new Shop(List.of(new Server(1,true,false,0)))).second() // (*) will not be tested</b>$.. ==&gt; 1.000 1 served by server 1<b>jshell&gt; // one busy server with no waiting customer</b><b>jshell&gt; new ArriveEvent(new Customer(1, 1.0)).execute(new Shop(List.of(new Server(1,false,false,1.0)))).first()</b>$.. ==&gt; [1 is busy; available at 1.000]<b>jshell&gt; new ArriveEvent(new Customer(1, 1.0)).execute(new Shop(List.of(new Server(1,false,false,1.0)))).second()</b>$.. ==&gt; 1.000 1 waits to be served by server 1<b>jshell&gt; // one busy server with waiting customer</b><b>jshell&gt; new ArriveEvent(new Customer(1, 1.0)).execute(new Shop(List.of(new Server(1,false,true,2.0)))).first()</b>$.. ==&gt; [1 is busy; waiting customer to be served at 2.000]<b>jshell&gt; new ArriveEvent(new Customer(1, 1.0)).execute(new Shop(List.of(new Server(1,false,true,2.0)))).second()</b>$.. ==&gt; 1.000 1 leaves<b>jshell&gt; /exit</b></pre>Note that an arrival event does not cause the servers in the shop to change, but only transits to another event depend on the state of the servers.<tt><b>(*)</b></tt> This test case will not be tested as it is not deterministic. In the case if the server rests (level 5) in between the arrival and serve events, then the server will actually not be able to serve.Just remember the following constraints:

    <ul>

     <li>every subclass of <tt>Event</tt> can only have properties, and at most <b>ONE</b> <tt>toString()</tt> method;</li>

     <li>every subclass of <tt>Event</tt> must not contain explicit loops.</li>

    </ul>As there could be varying ways in how your specific events are constructed, there will be no test cases. That said, you should still test out this new implementation of event generation on the inputs of the final level of Project #1 and check if the behaviour is the same.</td>

  </tr>

  <tr>

   <td><h4>Level 3</h4><big><strong>Randomizing arrival and service times</strong></big><b>From this level on, we shall test your implementation with the <tt>Main</tt> class. Make sure that all other classes reside in the <tt>cs2030.simulator</tt> package.</b>Input to the program comprises (in order of presentation):

    <ul>

     <li>an <tt>int</tt> value denoting the base seed for the <tt>RandomGenerator</tt> object;</li>

     <li>an <tt>int</tt> value representing the number of servers</li>

     <li>an <tt>int</tt> representing the number of customers (or the number of arrival events) to simulate</li>

     <li>a positive <tt>double</tt> parameter for the arrival rate, λ</li>

     <li>a positive <tt>double</tt> parameter for the service rate, μ</li>

    </ul>Remember to start the simulation by generating the first customer arrival event with timestamp <tt>0</tt>, and then generate the next arrival event of the next customer by adding the time generated using the method <tt>genInterArrivalTime()</tt> from the class <tt>RandomGenerator</tt>.The method <tt>genServiceTime()</tt> from the class can be used to generate the service time. Intuitively, the service time ought to be generated together with the arrival time as each customer is created before the simulation starts. However, do note that the <tt>n<sup>th</sup></tt> customer to arrive might not be the <tt>n<sup>th</sup></tt> customer to be served! So service time should be generated when the customer is being served, which might entail passing the <tt>RandomGenerator</tt> along with the Customer. This need not be so! <i>Hint: Think Lazy…</i>The following is a sample run of the program. Note that inputs are passed as command line arguments to <tt>Main</tt>.


    <table border="1">

     <tbody>

      <tr>

       <td><pre><b>$ java Main 1 1 5 1.0 1.0</b>0.000 1 arrives0.000 1 served by server 10.313 1 done serving by server 10.314 2 arrives0.314 2 served by server 10.417 2 done serving by server 11.205 3 arrives1.205 3 served by server 11.904 3 done serving by server 12.776 4 arrives2.776 4 served by server 12.791 4 done serving by server 13.877 5 arrives3.877 5 served by server 14.031 5 done serving by server 1[0.000 5 0]</pre></td>

      </tr>

      <tr>

       <td><pre><b>$ java Main 1 2 10 1.0 1.0</b>0.000 1 arrives0.000 1 served by server 10.313 1 done serving by server 10.314 2 arrives0.314 2 served by server 10.417 2 done serving by server 11.205 3 arrives1.205 3 served by server 11.904 3 done serving by server 12.776 4 arrives2.776 4 served by server 12.791 4 done serving by server 13.877 5 arrives3.877 5 served by server 13.910 6 arrives3.910 6 served by server 23.922 6 done serving by server 24.031 5 done serving by server 19.006 7 arrives9.006 7 served by server 19.043 8 arrives9.043 8 served by server 29.105 9 arrives9.105 9 waits to be served by server 19.160 10 arrives9.160 10 waits to be served by server 210.484 7 done serving by server 110.484 9 served by server 110.781 9 done serving by server 111.636 8 done serving by server 211.636 10 served by server 211.688 10 done serving by server 2[0.386 10 0]</pre></td>

      </tr>

     </tbody>

    </table></td>

  </tr>

  <tr>

   <td><h4>Level 4</h4><big><strong>Implement the customer queues</strong></big>Input to the program comprises (in order of presentation):

    <ul>

     <li>an <tt>int</tt> value denoting the base seed for the <tt>RandomGenerator</tt> object;</li>

     <li>an <tt>int</tt> value representing the number of servers</li>

     <li><b>an <tt>int</tt> value for the maximum queue length, <tt>Q<sub>max</sub></tt></b></li>

     <li>an <tt>int</tt> representing the number of customers (or the number of arrival events) to simulate</li>

     <li>a positive <tt>double</tt> parameter for the arrival rate, λ</li>

     <li>a positive <tt>double</tt> parameter for the service rate, μ</li>

    </ul>Now, each queue has a maximum capacity <tt>Q<sub>max</sub></tt>, and a customer cannot join a queue that is full. Note that a customer being served is not inside the queue. When a customer arrives and all the queues are full, then the customer leaves.Clearly, if <tt>Q<sub>max</sub> = 1</tt>, then the simulation reverts back to the one that allows only one waiting customer.The following is a sample run of the program.

    <table border="1">

     <tbody>

      <tr>

       <td><pre><b>$ java Main 1 2 2 10 1.0 1.0</b>0.000 1 arrives0.000 1 served by server 10.313 1 done serving by server 10.314 2 arrives0.314 2 served by server 10.417 2 done serving by server 11.205 3 arrives1.205 3 served by server 11.904 3 done serving by server 12.776 4 arrives2.776 4 served by server 12.791 4 done serving by server 13.877 5 arrives3.877 5 served by server 13.910 6 arrives3.910 6 served by server 23.922 6 done serving by server 24.031 5 done serving by server 19.006 7 arrives9.006 7 served by server 19.043 8 arrives9.043 8 served by server 29.105 9 arrives9.105 9 waits to be served by server 19.160 10 arrives9.160 10 waits to be served by server 110.484 7 done serving by server 110.484 9 served by server 110.781 9 done serving by server 110.781 10 served by server 110.833 10 done serving by server 111.636 8 done serving by server 2[0.300 10 0]</pre></td>

      </tr>

     </tbody>

    </table>Notice that the number of command line inputs is different from the previous level, as well as subsequent ones. In your <tt>Main</tt> class, you will need to identify the number of inputs, so as parse the inputs correctly for the correct level.</td>

  </tr>

  <tr>

   <td><h4>Level 5</h4><big><strong>Implementing server breaks</strong></big>Input to the program comprises (in order of presentation):

    <ul>

     <li>an <tt>int</tt> value denoting the base seed for the <tt>RandomGenerator</tt> object;</li>

     <li>an <tt>int</tt> value representing the number of servers</li>

     <li>an <tt>int</tt> value for the maximum queue length, <tt>Q<sub>max</sub></tt></li>

     <li>an <tt>int</tt> representing the number of customers (or the number of arrival events) to simulate</li>

     <li>a positive <tt>double</tt> parameter for the arrival rate, λ</li>

     <li>a positive <tt>double</tt> parameter for the service rate, μ</li>

     <li><strong>a positive <tt>double</tt> parameter for the resting rate, ρ</strong></li>

     <li><strong>a <tt>double</tt> parameter for the probability of resting, <tt>P<sub>r</sub></tt></strong></li>

    </ul>Include two events <tt>SERVER_REST</tt> and <tt>SERVER_BACK</tt>, to simulating taking a break, and returning. These events should be generated and scheduled in the simulator when the server decides to rest.To decide if the server should rest, a random number uniformly drawn from <tt>[0, 1]</tt> is generated using the <tt>RandomGenerator</tt> method <tt>genRandomRest()</tt>. If the value returned is less than <tt>P<sub>r</sub></tt>, the server rests with a <tt>SERVER_REST</tt> event generated. Otherwise, the server does not rest but continues serving the next customer.As soon as the server rests, a random rest period <tt>T<sub>r</sub></tt> is generated using the <tt>RandomGenerator</tt> method <tt>genRestPeriod()</tt>. This variable is an exponential random variable, governed by the resting rate, ρ. A <tt>SERVER_BACK</tt> event will be scheduled at <tt>T<sub>r</sub> + now</tt>.The following is a sample run of the program.

    <table border="1">

     <tbody>

      <tr>

       <td><pre><b>$ java Main 1 2 2 10 1.0 1.0 0 0</b>0.000 1 arrives0.000 1 served by server 10.313 1 done serving by server 10.314 2 arrives0.314 2 served by server 10.417 2 done serving by server 11.205 3 arrives1.205 3 served by server 11.904 3 done serving by server 12.776 4 arrives2.776 4 served by server 12.791 4 done serving by server 13.877 5 arrives3.877 5 served by server 13.910 6 arrives3.910 6 served by server 23.922 6 done serving by server 24.031 5 done serving by server 19.006 7 arrives9.006 7 served by server 19.043 8 arrives9.043 8 served by server 29.105 9 arrives9.105 9 waits to be served by server 19.160 10 arrives9.160 10 waits to be served by server 110.484 7 done serving by server 110.484 9 served by server 110.781 9 done serving by server 110.781 10 served by server 110.833 10 done serving by server 111.636 8 done serving by server 2[0.300 10 0]</pre></td>

      </tr>

      <tr>

       <td><pre><b>$ java Main 1 2 2 20 1.0 1.0 0.1 0.5</b>0.000 1 arrives0.000 1 served by server 10.313 1 done serving by server 10.314 2 arrives0.314 2 served by server 10.417 2 done serving by server 11.205 3 arrives1.205 3 served by server 21.904 3 done serving by server 22.776 4 arrives2.776 4 served by server 22.791 4 done serving by server 23.877 5 arrives3.877 5 served by server 13.910 6 arrives3.910 6 served by server 23.922 6 done serving by server 24.031 5 done serving by server 19.006 7 arrives9.006 7 served by server 19.043 8 arrives9.043 8 served by server 29.105 9 arrives9.105 9 waits to be served by server 19.160 10 arrives9.160 10 waits to be served by server 19.225 11 arrives9.225 11 waits to be served by server 210.148 12 arrives10.148 12 waits to be served by server 210.484 7 done serving by server 110.484 9 served by server 110.781 9 done serving by server 111.205 13 arrives11.205 13 waits to be served by server 111.636 8 done serving by server 211.636 11 served by server 211.688 11 done serving by server 211.688 12 served by server 212.429 14 arrives12.429 14 waits to be served by server 213.109 15 arrives13.109 15 waits to be served by server 214.644 10 served by server 115.013 10 done serving by server 115.178 12 done serving by server 215.178 14 served by server 215.264 16 arrives15.264 16 waits to be served by server 115.338 14 done serving by server 215.338 15 served by server 215.524 17 arrives15.524 17 waits to be served by server 215.940 18 arrives15.940 18 waits to be served by server 217.793 19 arrives17.793 19 leaves18.207 15 done serving by server 218.207 17 served by server 218.765 20 arrives18.765 20 waits to be served by server 219.103 17 done serving by server 219.103 18 served by server 220.146 18 done serving by server 240.474 13 served by server 140.480 13 done serving by server 140.480 16 served by server 141.056 16 done serving by server 157.110 20 served by server 257.852 20 done serving by server 2[6.025 19 1]</pre></td>

      </tr>

     </tbody>

    </table></td>

  </tr>

  <tr>

   <td><h4>Level 6</h4><big><strong>Include self-checkout counters</strong></big>Input to the program comprises (in order of presentation):

    <ul>

     <li>an <tt>int</tt> value denoting the base seed for the <tt>RandomGenerator</tt> object;</li>

     <li>an <tt>int</tt> value representing the number of servers</li>

     <li><strong>an <tt>int</tt> value representing the number of self-checkout counters, <tt>N<sub>self</sub></tt></strong></li>

     <li>an <tt>int</tt> value for the maximum queue length, <tt>Q<sub>max</sub></tt></li>

     <li>an <tt>int</tt> representing the number of customers (or the number of arrival events) to simulate</li>

     <li>a positive <tt>double</tt> parameter for the arrival rate, λ</li>

     <li>a positive <tt>double</tt> parameter for the service rate, μ</li>

     <li>a positive <tt>double</tt> parameter for the resting rate, ρ</li>

     <li>a <tt>double</tt> parameter for the probability of resting, <tt>P<sub>r</sub></tt></li>

    </ul>There are <tt>N<sub>self</sub></tt> self-checkout counters set up. In particular, if there are <tt>k</tt> human servers, then the self-checkout counters are identified from <tt>k + 1</tt> onwards. All self-checkout counters share the same queue. When we print out the wait event, we always say that the customer is waiting for the self-checkout counter <tt>k + 1</tt>, even though this customer may eventually be served by another self-checkout counter.The following is a sample run of the program.

    <table border="1">

     <tbody>

      <tr>

       <td><pre><b>$ java Main 1 2 0 2 20 1.0 1.0 0.1 0.5</b>0.000 1 arrives0.000 1 served by server 10.313 1 done serving by server 10.314 2 arrives0.314 2 served by server 10.417 2 done serving by server 11.205 3 arrives1.205 3 served by server 21.904 3 done serving by server 22.776 4 arrives2.776 4 served by server 22.791 4 done serving by server 23.877 5 arrives3.877 5 served by server 13.910 6 arrives3.910 6 served by server 23.922 6 done serving by server 24.031 5 done serving by server 19.006 7 arrives9.006 7 served by server 19.043 8 arrives9.043 8 served by server 29.105 9 arrives9.105 9 waits to be served by server 19.160 10 arrives9.160 10 waits to be served by server 19.225 11 arrives9.225 11 waits to be served by server 210.148 12 arrives10.148 12 waits to be served by server 210.484 7 done serving by server 110.484 9 served by server 110.781 9 done serving by server 111.205 13 arrives11.205 13 waits to be served by server 111.636 8 done serving by server 211.636 11 served by server 211.688 11 done serving by server 211.688 12 served by server 212.429 14 arrives12.429 14 waits to be served by server 213.109 15 arrives13.109 15 waits to be served by server 214.644 10 served by server 115.013 10 done serving by server 115.178 12 done serving by server 215.178 14 served by server 215.264 16 arrives15.264 16 waits to be served by server 115.338 14 done serving by server 215.338 15 served by server 215.524 17 arrives15.524 17 waits to be served by server 215.940 18 arrives15.940 18 waits to be served by server 217.793 19 arrives17.793 19 leaves18.207 15 done serving by server 218.207 17 served by server 218.765 20 arrives18.765 20 waits to be served by server 219.103 17 done serving by server 219.103 18 served by server 220.146 18 done serving by server 240.474 13 served by server 140.480 13 done serving by server 140.480 16 served by server 141.056 16 done serving by server 157.110 20 served by server 257.852 20 done serving by server 2[6.025 19 1]</pre></td>

      </tr>

      <tr>

       <td><pre><b>$ java Main 1 2 1 2 20 1.0 1.0 0.1 0.5</b>0.000 1 arrives0.000 1 served by server 10.313 1 done serving by server 10.314 2 arrives0.314 2 served by server 10.417 2 done serving by server 11.205 3 arrives1.205 3 served by server 21.904 3 done serving by server 22.776 4 arrives2.776 4 served by server 22.791 4 done serving by server 23.877 5 arrives3.877 5 served by server 13.910 6 arrives3.910 6 served by server 23.922 6 done serving by server 24.031 5 done serving by server 19.006 7 arrives9.006 7 served by server 19.043 8 arrives9.043 8 served by server 29.105 9 arrives9.105 9 served by self-check 39.160 10 arrives9.160 10 waits to be served by server 19.225 11 arrives9.225 11 waits to be served by server 19.402 9 done serving by self-check 310.148 12 arrives10.148 12 served by self-check 310.200 12 done serving by self-check 310.484 7 done serving by server 110.484 10 served by server 111.205 13 arrives11.205 13 served by self-check 311.574 13 done serving by self-check 311.636 8 done serving by server 212.429 14 arrives12.429 14 served by self-check 312.589 14 done serving by self-check 313.109 15 arrives13.109 15 served by self-check 313.974 10 done serving by server 113.974 11 served by server 114.869 11 done serving by server 115.264 16 arrives15.264 16 served by server 115.524 17 arrives15.524 17 served by server 215.531 17 done serving by server 215.940 18 arrives15.940 18 waits to be served by server 115.978 15 done serving by self-check 316.307 16 done serving by server 116.307 18 served by server 116.883 18 done serving by server 117.793 19 arrives17.793 19 served by server 118.535 19 done serving by server 118.765 20 arrives18.765 20 served by server 121.773 20 done serving by server 1[0.322 20 0]</pre></td>

      </tr>

      <tr>

       <td><pre><b>$ java Main 1 1 2 2 20 1.0 0.1 0.1 0.5</b>0.000 1 arrives0.000 1 served by server 10.314 2 arrives0.314 2 served by self-check 21.205 3 arrives1.205 3 served by self-check 31.351 2 done serving by self-check 22.776 4 arrives2.776 4 served by self-check 22.919 4 done serving by self-check 23.131 1 done serving by server 13.877 5 arrives3.877 5 served by server 13.910 6 arrives3.910 6 served by self-check 24.036 6 done serving by self-check 25.419 5 done serving by server 18.200 3 done serving by self-check 39.006 7 arrives9.006 7 served by server 19.043 8 arrives9.043 8 served by self-check 29.105 9 arrives9.105 9 served by self-check 39.160 10 arrives9.160 10 waits to be served by server 19.225 11 arrives9.225 11 waits to be served by server 110.148 12 arrives10.148 12 waits to be served by self-check 211.205 13 arrives11.205 13 waits to be served by self-check 212.074 9 done serving by self-check 312.074 12 served by self-check 312.429 14 arrives12.429 14 waits to be served by self-check 212.591 12 done serving by self-check 312.591 13 served by self-check 313.109 15 arrives13.109 15 waits to be served by self-check 215.264 16 arrives15.264 16 leaves15.524 17 arrives15.524 17 leaves15.940 18 arrives15.940 18 leaves17.793 19 arrives17.793 19 leaves18.765 20 arrives18.765 20 leaves23.784 7 done serving by server 124.631 10 served by server 128.318 10 done serving by server 128.318 11 served by server 129.923 11 done serving by server 134.974 8 done serving by self-check 234.974 14 served by self-check 247.487 13 done serving by self-check 347.487 15 served by self-check 356.445 15 done serving by self-check 363.665 14 done serving by self-check 2[6.320 15 5]</pre></td>

      </tr>

     </tbody>

    </table></td>

  </tr>

  <tr>

   <td><h4>Level 7</h4><big><strong>Include greedy customers</strong></big>Input to the program comprises (in order of presentation):

    <ul>

     <li>an <tt>int</tt> value denoting the base seed for the <tt>RandomGenerator</tt> object;</li>

     <li>an <tt>int</tt> value representing the number of servers</li>

     <li>an <tt>int</tt> value representing the number of self-checkout counters, <tt>N<sub>self</sub></tt></li>

     <li>an <tt>int</tt> value for the maximum queue length, <tt>Q<sub>max</sub></tt></li>

     <li>an <tt>int</tt> representing the number of customers (or the number of arrival events) to simulate</li>

     <li>a positive <tt>double</tt> parameter for the arrival rate, λ</li>

     <li>a positive <tt>double</tt> parameter for the service rate, μ</li>

     <li>a positive <tt>double</tt> parameter for the resting rate, ρ</li>

     <li>a <tt>double</tt> parameter for the probability of resting, <tt>P<sub>r</sub></tt></li>

     <li><strong>a <tt>double</tt> parameter for the probability of a greedy customer occurring, <tt>P<sub>g</sub></tt></strong></li>

    </ul>An arriving customer is a greedy customer with probability <tt>P<sub>g</sub></tt>. To decide whether a typical or greedy customer is created, a random number uniformly drawn from <tt>[0, 1]</tt> is generated with the <tt>RandomGenerator</tt> method <tt>genCustomerType()</tt>. If the value returned is less than <tt>P<sub>g</sub></tt>, a greedy customer is generated, otherwise, a typical customer is generated.The following is a sample run of the program.

    <table border="1">

     <tbody>

      <tr>

       <td><pre><b>$ java Main 1 2 1 2 20 1.0 1.0 0.1 0.5 0</b>0.000 1 arrives0.000 1 served by server 10.313 1 done serving by server 10.314 2 arrives0.314 2 served by server 10.417 2 done serving by server 11.205 3 arrives1.205 3 served by server 21.904 3 done serving by server 22.776 4 arrives2.776 4 served by server 22.791 4 done serving by server 23.877 5 arrives3.877 5 served by server 13.910 6 arrives3.910 6 served by server 23.922 6 done serving by server 24.031 5 done serving by server 19.006 7 arrives9.006 7 served by server 19.043 8 arrives9.043 8 served by server 29.105 9 arrives9.105 9 served by self-check 39.160 10 arrives9.160 10 waits to be served by server 19.225 11 arrives9.225 11 waits to be served by server 19.402 9 done serving by self-check 310.148 12 arrives10.148 12 served by self-check 310.200 12 done serving by self-check 310.484 7 done serving by server 110.484 10 served by server 111.205 13 arrives11.205 13 served by self-check 311.574 13 done serving by self-check 311.636 8 done serving by server 212.429 14 arrives12.429 14 served by self-check 312.589 14 done serving by self-check 313.109 15 arrives13.109 15 served by self-check 313.974 10 done serving by server 113.974 11 served by server 114.869 11 done serving by server 115.264 16 arrives15.264 16 served by server 115.524 17 arrives15.524 17 served by server 215.531 17 done serving by server 215.940 18 arrives15.940 18 waits to be served by server 115.978 15 done serving by self-check 316.307 16 done serving by server 116.307 18 served by server 116.883 18 done serving by server 117.793 19 arrives17.793 19 served by server 118.535 19 done serving by server 118.765 20 arrives18.765 20 served by server 121.773 20 done serving by server 1[0.322 20 0]</pre></td>

      </tr>

      <tr>

       <td><pre><b>$ java Main 1 2 1 2 20 1.0 1.0 0.1 0.5 0.9</b>0.000 1(greedy) arrives0.000 1(greedy) served by server 10.313 1(greedy) done serving by server 10.314 2(greedy) arrives0.314 2(greedy) served by server 10.417 2(greedy) done serving by server 11.205 3(greedy) arrives1.205 3(greedy) served by server 21.904 3(greedy) done serving by server 22.776 4(greedy) arrives2.776 4(greedy) served by server 22.791 4(greedy) done serving by server 23.877 5(greedy) arrives3.877 5(greedy) served by server 13.910 6(greedy) arrives3.910 6(greedy) served by server 23.922 6(greedy) done serving by server 24.031 5(greedy) done serving by server 19.006 7(greedy) arrives9.006 7(greedy) served by server 19.043 8(greedy) arrives9.043 8(greedy) served by server 29.105 9(greedy) arrives9.105 9(greedy) served by self-check 39.160 10 arrives9.160 10 waits to be served by server 19.225 11(greedy) arrives9.225 11(greedy) waits to be served by server 29.402 9(greedy) done serving by self-check 310.148 12(greedy) arrives10.148 12(greedy) served by self-check 310.200 12(greedy) done serving by self-check 310.484 7(greedy) done serving by server 110.484 10 served by server 111.205 13(greedy) arrives11.205 13(greedy) served by self-check 311.574 13(greedy) done serving by self-check 311.636 8(greedy) done serving by server 212.429 14(greedy) arrives12.429 14(greedy) served by self-check 312.589 14(greedy) done serving by self-check 313.109 15(greedy) arrives13.109 15(greedy) served by self-check 313.974 10 done serving by server 115.264 16 arrives15.264 16 served by server 115.500 11(greedy) served by server 215.524 17(greedy) arrives15.524 17(greedy) waits to be served by server 115.940 18(greedy) arrives15.940 18(greedy) waits to be served by server 215.978 15(greedy) done serving by self-check 316.159 16 done serving by server 116.159 17(greedy) served by server 116.166 17(greedy) done serving by server 116.543 11(greedy) done serving by server 216.543 18(greedy) served by server 217.119 18(greedy) done serving by server 217.793 19(greedy) arrives17.793 19(greedy) served by server 218.535 19(greedy) done serving by server 218.765 20(greedy) arrives18.765 20(greedy) served by server 221.773 20(greedy) done serving by server 2[0.442 20 0]</pre></td>

      </tr>

     </tbody>

    </table></td>

  </tr>

 </tbody>

</table>