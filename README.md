Download Link: https://assignmentchef.com/product/solved-64831-assignment-2-reliable-transport-with-selective-repeat
<br>
Adapted from Kurose &amp; Ross – Computer Networking: a top-down approach featuring the Internet

<strong>CBOK categories: Abstraction, Design, Data &amp; Information, Networking, Programming</strong>

<h1>Foreword</h1>

This practical requires thought and planning. You need to start early to allow yourself time to think of what and how to test before modifying any code. Failing to do this is likely to make the practical take far more time than it should. Starting the practical in the final week is likely to be an unsuccessful strategy with this practical.

<h1>Very Important</h1>

<em>Although this practical is marked automatically, all marks will be moderated by a marker using the following information.</em>

You must add a comment explaining the changes you’ve made to <strong><em>each</em></strong> of your svn repository commits. In other words, each commit should explain what was changed in the code and why briefly (e.g. it may be a bug fix or a protocol behaviour fix).

We should be able to review your development process and see the changes made to code with each version you have committed. <strong>Please keep in mind that we can check the number of lines of code as well as actual code changes made with each commit.</strong>

During manual marking and plagiarisms checks, we will look look at your development process. If we do not see a clear path to a solution (i.e. code changes and regular commits and comments reflecting your learning to develop the protocol you <strong>will forfeit 50% of the marks allocated for this prac and in the extreme case you will be allocated a mark of  0%. An example case of a 0% would be the sudden appearance of working code passing multiple tests</strong>.

It is up to you to provide evidence of your development process.

<strong><em>Please make use of the emulator to help you learn!</em></strong>

<h2>Task</h2>

Implement reliable transport protocol using Selective Repeat in C by studying and extending an implmentation of Go Back N running on a network simulator.

<h2>Background</h2>

You’ve been hired by “Net Source”, a company specialising in networking software to build Selective Repeat code for their network emulation tool. They have an existing implementation of Go-Back-N that interfaces with their emulator. Their programmer, Mue, who was working on an implementation of the Selective Repeat protocol has caught the flu and the implementation must absolutely be finished in the next week.  As the code isn’t finished, Mue hasn’t completed testing yet. Mue is an experienced C programmer and there is nothing wrong with the C syntax or structure of the code that Mue has written. So you don’t have to correct any C syntax errors, your job is to finish the code, correct any <strong><em>protocol</em></strong> errors and test it.

Although you are not required to use this code, they expect much of this code is reusable and only some modifications are needed to change Go-Back-N to Selective Repeat. Your boss realises you’re not a C expert, but at least you’ve programmed in Java or C++ before so you’re the closest they have as an expert. Their last C programmer has written a <a href="https://myuni.adelaide.edu.au/courses/64831/pages/c-hints"><strong>C hints for Java and C++ pro</strong></a><a href="https://myuni.adelaide.edu.au/courses/64831/pages/c-hints"><strong>g</strong></a><a href="https://myuni.adelaide.edu.au/courses/64831/pages/c-hints"><strong>rammers</strong></a> sheet which explains what you need to know about C that is different than Java and C++. She’s confident that if you stick to the sheet, anything else you need to add or change would be the same if you wrote it in Java or C++.

<h2>System Overview</h2>

To help isolate and demonstrate the behaviour of the sender and receiver, you have a simulation system at your disposal. The overall structure of the environment is shown below:

There are two hosts (A and B). An application on host A is sending messages to an application on host B. The application messages (layer 5) on host A are sent to layer 4 (transport) on host A, which must implement the reliable transport protocol for reliable delivery. Layer 4 creates packets and sends them to the network (layer 3). The network transfers (unreliably) these packets to host B where they are handed to the transport layer ( receiver) and if not corrupt or out of order, the message is extracted and delivered to the receiving application (layer 5) on host B.

The file gbn.c has the code for the Go Back N sender procedures A_output() , A_init() , A_input() , and A_timerinterrupt() . gbn.c also has the code for the Go Back N receiver procedures B_input() and B_init() . At this stage, only unidirectional transfer of data (from A to B) is required, so B does not need to implement B_timerinterrupt() . Of course, B will have to send packets to A to acknowledge receipt of data.

<h2>Go Back N Software Interface</h2>

The routines are detailed below. Such procedures in real-life would be part of the operating system, and would be called by other procedures in the operating system. In the simulator the simulator will call and be called by procedures that emulate the network environment and operating system.

message is a structure containing data to be sent to B. This routine will be called whenever the upper

layer application at the sending side (A) has a message to send.  It is the job of the reliable transport protocol to insure that the data in such a message is delivered in-order, and correctly, to the receiving side upper layer.

This routine will be called whenever a packet sent from B (i.e., as a result of a <em>tolayer3()</em> being called by a B procedure) arrives at A. <em>packet</em> is the (possibly corrupted) packet sent from B.

This routine will be called once, before any other A-side routines are called. It is used to do any required initialization.

This routine will be called whenever a packet sent from A (i.e., as a result of a  <em>tolayer3()</em> being called by a A-side procedure) arrives at B. The <em>packet</em> is the (possibly corrupted) packet sent from A.

This routine will be called once, before any other B-side routines are called. It is used to do any required initialization.

The unit of data passed between the application layer and the transport layer protocol is a message<em>,</em> which is declared as:

The sending entity will thus receive data in 20-byte chunks from the sending application, and the receiving entity should deliver 20-byte chunks to the receiving application.

The A_output() routine fills in the payload field from the message data passed down from the Application layer. The other packet fields must be filled in the a_output routine so that they can be used by the reliable transport protocol to insure reliable delivery, as we’ve seen in class.

These functions implement what the sender and receiver should do when packets arrive.

<h3>The Emulator Software Interface</h3>

This section will describe the functions of the emulator code that you will be using in your implementation. <strong>Do not edit the emulator code.</strong>

The procedures described above implement the reliable transport layer protocol and are the procedures that you will be implementing.

The following emulator procedures can be called by the reliable transport procedures you write. They are explained here so you know how they fit in. They are <em>not</em> part of the reliable transport implementation and these routines work correctly.

<h4>1 void starttimer(int calling_entity, double);</h4>

Where calling_entity is either A (for starting the A-side timer) or B (for starting the B side timer), and increment is a <em>float</em> value indicating the amount of time that will pass before the timer interrupts. A’s timer should only be started (or stopped) by A-side routines, and similarly for the B-side timer. To give you an idea of the appropriate increment value to use: a packet sent into the network takes an average of 5 time units to arrive at the other side when there are no other messages in the medium. You are free to experiment with different timeout values; but <em>when handing in, the timeout value must be set to 15.0</em>

Note that starttimer() is not the same as restarttimer() . If a timer is already running it must be stopped before it is started. Calling starttimer() when the timer is already running, or calling stoptimer() when the timer is not running, indicates an error in the protocol behaviour and will result

in an error message.

The emulator code is in the file emulator.c .Use the emulator header file to refere to the definitions of the emulator functions. You do not need to understand or look at emulator.c ; but if you want to know how the emulator works, you are welcome to look at the code.

Where calling_entity is either A (for A-side delivery to layer 5) or B (for B-side delivery to layer 5).

With unidirectional data transfer, you would only be calling this when calling_entity is equal to B (delivery to the B-side). Calling this routine will cause data to be passed up to layer 5.

Download the following 4 files and examine the code in gbn.c with the description above and your knowledge of Go Back N to make sure you understand how the program fits together:

<strong><u> </u></strong><a href="https://myuni.adelaide.edu.au/courses/64831/files/8364872/download?download_frd=1"><strong>emulator.c</strong></a> <strong>        </strong><a href="https://myuni.adelaide.edu.au/courses/64831/files/8364872/download?download_frd=1"><strong> </strong></a><a href="https://myuni.adelaide.edu.au/courses/64831/files/8364872/download?download_frd=1"><strong>(https://myuni.adelaide.edu.au/courses/64831/files/8364872/download?</strong></a>

<a href="https://myuni.adelaide.edu.au/courses/64831/files/8364872/download?download_frd=1"><strong>download_frd=1) </strong></a><strong><u> </u></strong><a href="https://myuni.adelaide.edu.au/courses/64831/files/8364871/download?download_frd=1"><strong>emulator.h</strong></a> <strong>           </strong><a href="https://myuni.adelaide.edu.au/courses/64831/files/8364871/download?download_frd=1"><strong> </strong></a><a href="https://myuni.adelaide.edu.au/courses/64831/files/8364871/download?download_frd=1"><strong>(https://myuni.adelaide.edu.au/courses/64831/files/8364871/download?</strong></a>

<a href="https://myuni.adelaide.edu.au/courses/64831/files/8364871/download?download_frd=1"><strong>download_frd=1)</strong></a>

<a href="https://myuni.adelaide.edu.au/courses/64831/files/8364735/download?wrap=1"><strong>g</strong></a><a href="https://myuni.adelaide.edu.au/courses/64831/files/8364735/download?wrap=1"><strong>bn.c</strong></a><a href="https://myuni.adelaide.edu.au/courses/64831/files/8364735/download?download_frd=1"><strong>  </strong></a><a href="https://myuni.adelaide.edu.au/courses/64831/files/8364735/download?download_frd=1"><strong>(https://myuni.adelaide.edu.au/courses/64831/files/8364735/download?download_frd=1) </strong></a><a href="https://myuni.adelaide.edu.au/courses/64831/files/8364736/download?wrap=1"><strong>g</strong></a><a href="https://myuni.adelaide.edu.au/courses/64831/files/8364736/download?wrap=1"><strong>bn.h</strong></a><a href="https://myuni.adelaide.edu.au/courses/64831/files/8364736/download?download_frd=1"><strong>  </strong></a><a href="https://myuni.adelaide.edu.au/courses/64831/files/8364736/download?download_frd=1"><strong>(https://myuni.adelaide.edu.au/courses/64831/files/8364736/download?download_frd=1)</strong></a>

To build the program use the command:

<h2>Running the simulated network</h2>

The emulator is capable of corrupting and losing packets. It will not reorder packets. When you compile and run the resulting program, you will be asked to specify values regarding the simulated network environment

For example

<h3>1 $ ./gbn</h3>

2 —–  Stop and Wait Network Simulator Version 1.1 ——–

3

4 Enter the number of messages to simulate: 10 5 Enter packet loss probability [enter 0.0 for no loss]:0 6 Enter packet corruption probability [0.0 for no corruption]:0 7 Enter average time between messages from sender’s layer5 [ &gt; 0.0]:10 8 Enter TRACE:2




<h3>Number of messages to simulate</h3>

The emulator will stop generating messages as soon as this number of messages have been passed down from layer 5.

<h3>Loss</h3>

You are asked to specify a packet loss probability. A value of 0.1 would mean that one in ten packets (on average) are lost.

<h3>Corruption</h3>

You are asked to specify a packet corruption probability. A value of 0.2 would mean that one in five packets (on average) are corrupted. Note that the contents of payload, sequence or ack field can be corrupted.

<h3>Average time between messages from sender’s layer5</h3>

You can set this value to any non-zero, positive value. Note that the smaller the value you choose, the faster packets will be generated.

<h3>Tracing</h3>

Setting a tracing value of 1 or 2 will print out useful information about what is going on inside the emulation (e.g., what’s happening to packets and timers). A tracing value of 0 will turn this off. A tracing value greater than 2 will display all sorts of messages that detail what is happening inside the emulation code as well. A tracing value of 2 may be helpful to you in debugging your code. You should keep in mind that <em>real</em> implementors do not have underlying networks that provide such nice information about what is going to happen to their packets!

<strong><em>Take a moment to experiment with the simulated environment and look at the events that occur. Try sending a few packets with no loss or corruption. Does the protocol work properly?</em></strong>

<strong><em>Try with just loss. Try with just corruption. </em></strong>

<h2>Selective Repeat</h2>

Once you have inspected Go Back N and understand how the implementation works, you need to study carefully how Selective Repeat (SR) differs from Go Back N. Consider how GBN and SR respond to ACKs, timeouts, new messages, etc on both the sender and the receiver side. In what cases is their behavior the same and in what cases is it different.

When you have considered the similarities and differences, you should be able to identify what changes you will need to make to the existing GBN implementation in order to implement SR. Copy the gbn.c file to a file called sr.c and copy gbn.h to sr.h. Design your changes and then implement those changes in your sr.c file.

The implementation of Selective Repeat <em>should</em> be identical to that described in the textbook (Section:

Principles of Reliable Data Transfer)

When you have completed your selective repeat implementation you can compile it with:

<h2>Some Tips</h2>

<h3>Compiler flags</h3>

When you recompile the program. Using the -ansi -Wall -pedantic flags will give you useful warnings if you are doing something that is likely to be wrong (like using = rather than ==, or misusing a data structure). If you get any warnings, fix them before submitting. Warnings indicate that there is something wrong, even if the program compiles. If you can’t work out the warning, ask on the discussion board.

<h3>Code – Compile – Test – Repeat</h3>

Make one change at a time and devise a test case to check that your addition actually works. For example, if you wanted to check that B was responding correctly to an out of order packet, send a few (say 2) packets with a loss probability of .2 and see how B responds. If none of the packets are lost, then increase the loss probability until you get a case where one of the packets is lost and a packet arrives out of order so you can see B’s response.

Be sure to commit your changes to svn or they won’t be tested

Once you’re confident you have the selective repeat protocol working, or if you are really stuck on what is wrong, submit to websubmission system. A test script will run a series of tests specifically designed to identify whether the protocol is behaving as expected. If your corrected program does not pass one of the tests, The test script will stop at that point and show you the expected output of the test and your output. By comparing the two you should be able to work out at least one thing that the protocol is still doing incorrectly.

The testing script we run is not a program with high-level reasoning abilities, it’s possible that you might do something unexpected that will trick it. Inspecting the output should allow you to identify whether the problem is with your implementation or just an unexpected output.

<h3>What you “C” is what you get</h3>

This programming assignment is in the C language. Most networking system code and many networked applications are written in C, so we consider it important that you have at least seen C and can do some basic reading and debugging in the language. The code is given and the syntax of C and Java are very similar/same as are the basic structures (if/then, loops, etc), so we do expect that third year students can accomplish this. The main C concepts that you may not have come across in

<a href="https://cs.adelaide.edu.au/users/third/cn/Practicals/Chints.html">Java will be accessing structures and handling pointers. We have provided a </a><a href="https://cs.adelaide.edu.au/users/third/cn/Practicals/Chints.html"><strong>C hints</strong></a>

<a href="https://cs.adelaide.edu.au/users/third/cn/Practicals/Chints.html"><strong>(https://cs.adelaide.edu.au/users/third/cn/Practicals/Chints.html) </strong></a><a href="https://cs.adelaide.edu.au/users/third/cn/Practicals/Chints.html">sheet which explains wh</a>at you need to know that is different than Java. Unless you are familiar with C, please stick to our hints. It’s easy to get memory manipulation wrong in C and it can be quite difficult to debug. The code has been carefully structured so that you do not need to make use of pointers. If you are experienced with C, you can use pointers, if you are not experienced with C, then stick to static arrays as the GBN implementation does. The hint sheet gives you the easy/clear way of doing the things. Remember that the given code is correct C, so you can use the given code as examples for how to manage the arrays and structures. If you do have any questions still after looking at the examples, please don’t hesitate to post questions (even code fragments) on the discussion board.