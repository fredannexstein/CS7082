# CS7082

## The Byzantine Generals Problem

The Byzantine Generals Problem seems deceptively simple. Its difficulty is
indicated by the surprising fact that if the generals can send only oral messages,
then no solution will work unless more than two-thirds of the generals are loyal.
In particular, with only three generals, no solution can work in the presence of a
single traitor. An oral message is one whose contents are completely under the
control of the sender, so a traitorous sender can transmit any possible message.
Such a message corresponds to the type of message that computers normally
send to one another. In Section 4 we consider signed, written messages, for which
this is not true. 

## The Two generals Problem
This problem (first published in 1975 and given its name in 1978) describes a scenario where two generals are attacking a common enemy. General 1 is considered the leader and the other is considered the follower. Each general’s army on its own is not enough to defeat the enemy army successfully, thus they need to cooperate and attack at the same time. This seems like a simple scenario, but there is one caveat:

In order for them to communicate and decide on a time, General 1 has to send a messenger across the enemy’s camp that will deliver the time of the attack to General 2. However, there is a possibility that the messenger will get captured by the enemies and thus the message won’t be delivered. That will result in General 1 attacking while General 2 and his army hold their grounds.

Even if the first message goes through, General 2 has to acknowledge (ACK, notice the similarity to the 3-way handshake of TCP ) that he received the message, so he sends a messenger back, thus repeating the previous scenario where the messenger can get caught. This extends to infinite ACK’s and thus the generals are unable to reach an agreement.

There is no way to guarantee the second requirement that each general be sure the other has agreed to the attack plan. Both generals will always be left wondering whether their last messenger got through.

Since the possibility of the message not getting through is always > 0, the generals can never reach an aggrement with 100% confidence.
The Two Generals Problem has been proven to be unsolvable.

The Byzantine Generals Problem
Famously described in 1982 by Lamport, Shostak and Pease, it is a generalized version of the Two Generals Problem with a twist. It describes the same scenario, where instead more than two generals need to agree on a time to attack their common enemy. The added complication here is that one or more of the generals can be a traitor, meaning that they can lie about their choice (e.g. they say that they agree to attack at 0900 but instead they do not).

The leader-follower paradigm described in the Two Generals Problem is transformed to a commander-lieutenant setup. In order to achieve consensus here, the commander and every lieutenant must agree on the same decision (for simplicity attack or retreat).


page 3, The Byzantine Generals Problem
Adding to IC2., it gets interesting that if the commander is a traitor, consensus must still be achieved. As a result, all lieutenants take the majority vote.

The algorithm to reach consensus in this case is based on the value of majority of the decisions a lieutenant observes.

Theorem: For any m, Algorithm OM(m) reaches consensus if there are more than 3m generals and at most m traitors.
This implies that the algorithm can reach consensus as long as 2/3 of the actors are honest. If the traitors are more than 1/3, consensus is not reached, the armies do not coordinate their attack and the enemy wins.


m = 0 → no traitors, each lieutenant obeys | m > 0 → each lieutenant’s final choice comes from the majority of all lieutenant’s choices
This should be more clear with a visual example from Lieutentant 2’s point of view— Let C be Commander and L{i} be Lieutenant i:


OM(1): Lieutenant 3 is a traitor — L2 point of view
Steps:

Commander sends v to all Lieutenants
L1 sends v to L2 | L3 sends x to L2
L2 ← majority(v,v,x) == v
The final decision is the majority vote from L1, L2, L3 and as a result consensus has been achieved

The important thing to remember is that the goal is for the majority of the lieutenants to choose the same decision, not a specific one.
Let’s examine the case of the commander being a traitor:


OM(1): Commander is a traitor
Steps:

Commander sends x, y, z to L1, L2, L3 respectively
L1 sends x to L2, L3 | L2 sends y to L1, L3 | L3 sends z to L1, L2
L1 ← majority(x,y,z) | L2 ← majority(x,y,z) | L3 ← majority(x,y,z)
They all have the same value and thus consensus is reached. Take a moment here to reflect that even if x, y, z are all different the value of majority(x, y, z) is the same for all 3 Lieutenants. In the case x,y,z are totally different commands, we can assume that they act on the default option retreat.

For a more hands-on approach and a more complex example with 7 generals and 2 traitors, I suggest you read this article.

Byzantine Fault Tolerance
Byzantine Fault Tolerance is the characteristic which defines a system that tolerates the class of failures that belong to the Byzantine Generals’ Problem. Byzantine Failure is the most difficult class of failure modes. It implies no restrictions, and makes no assumptions about the kind of behavior a node can have (e.g. a node can generate any kind of arbitrary data while posing as an honest actor).

Byzantine Faults are the most severe and difficult to deal with. Byzantine Fault Tolerance has been needed in airplane engine systems, nuclear power plants and pretty much any system whose actions depend on the results of a large amount of sensors. Even SpaceX was considering it as a potential requirement for their systems.

The algorithm mentioned in the previous section is Byzantine Fault Tolerant as long as the number of traitors do not exceed one third of the generals. Other variations exist which make solving the problem easier, including the use of digital signatures or by imposing communication restrictions between the peers in the network.




The Byzantine Generals Problem is an agreement protocol that's built around an imaginary General who makes a decision to attack or retreat, and who must communicate his decision to his lieutenants.
Mark is a contributing editor to Dr. Dobb's Journal and author of The Data Compression Book. He can be contacted at http://marknelson.us/.
The Byzantine General's problem is one of many in the field of agreement protocols. In 1982, Leslie Lamport described this problem in a paper written with Marshall Pease and Robert Shostak. Lamport framed the paper around a story problem after observing what he felt was an inordinate amount of attention received by Dijkstra's Dining Philosopher problem.

This problem is built around an imaginary General who makes a decision to attack or retreat, and must communicate the decision to his lieutenants. A given number of these actors are traitors (possibly including the General). Traitors cannot be relied upon to properly communicate orders; worse yet, they may actively alter messages in an attempt to subvert the process.

The generals are collectively known as processes. The general who initiates the order is the source process, and the orders sent to the other processes are messages. Traitorous generals and lieutenants are faulty processes, and loyal generals and lieutenants are correct processes. The order to retreat or attack is a message with a 1 or 0.

In general, a solution to agreement problems must pass three tests: termination, agreement, and validity. As applied to the Byzantine General's problem, these three tests are:

A solution has to guarantee that all correct processes eventually reach a decision regarding the value of the order they have been given.
All correct processes have to decide on the same value of the order they have been given.
If the source process is a correct process, all processes have to decide on the value that was originally given by the source process.
One side effect of this is that if the source process is faulty, all other processes still have to agree on the same value. It doesn't matter what value they agree on, they simply all have to agree. So if the General is subversive, all lieutenants still have to come to a common, unanimous decision.

Difficulties
This agreement problem doesn't lend itself to an easy solution. Imagine, for example, that the source process is the only faulty process. It tells half the processes that the value of their order is 0, and the other half that their value is 1.

After receiving the order from the source process, the remaining processes have to agree on a value that they will all decide on. The processes could quickly poll one another to see what value they received from the source process.

In this scenario, imagine the decision algorithm of a process that receives an initial message of 0 from the source process, but sees that one of the other processes says that the correct value is 1. Given the conflict, the process knows that either the source process is faulty, having given different values to two different peers, or the peer is faulty, and is lying about the value it received from the source process.

It's fine to reach the conclusion that someone is lying, but making a final decision on who is the traitor seems to be an insurmountable problem. And in fact, it can be proven that it is impossible to decide in some cases. The classic example used to show this is when there are only three processes: One source process and two peer processes.

In the configurations in Figures 1 and 2, the peer processes attempt to reach consensus by sending each other their proposed value after receiving it from the source process. In Figure 1, the source process (P1) is faulty, sending two different values to the peers. In Figure 2, P3 is faulty, sending an incorrect value to the peer.


Figure 1: The case in which the source process is faulty.

Figure 2: The case in which P3 is faulty.
You can see the difficulty P2 faces in this situation. Regardless of which configuration it is in, the incoming data is the same. P2 has no way to distinguish between the two configurations, and no way to know which of the two other processes to trust.

This situation doesn't necessarily get better just by throwing more nonfaulty processes at the problem. A naïve algorithm (as in Figures 1 and 2) would have each process tell every other process what it received from P1. A process would decide the correct decision by simple majority.

It's relatively easy to show that, regardless of how many processes are in the system. A subversive source process with one collaborator can cause half the processes to choose to attack, and half the processes to retreat, leading to maximum confusion.

# The Lamport, Pease, and Shostak Algorithm
In 1982, Lamport, Pease, and Shostak published a straightforward solution to this problem (research.microsoft.com/users/ lamport/pubs/byz.pdf). The algorithm assumes that there are n processes, with m faulty processes, where n>3m. Thus, for a scenario such as that in Figures 1 and 2 with one faulty process, there would have to be a minimum of four processes in the system to come to agreement. (For purposes here, n refers to the count of processes, and m to the number of faulty processes.)

The definition of the algorithm in the original paper is short and succinct, but can be confusing for programmers who don't have experience with distributed algorithms.

Lamport's algorithm is a recursive definition, with a base case for m=0, and a recursive step for m>0:

r	Algorithm OM(0)
The general sends his value to every lieutenant.
Each lieutenant uses the value he receives from the general.
r	Algorithm OM(m), m>0
The general sends his value to each lieutenant.
For each i, let vi be the value lieutenant i receives from the general. Lieutenant i acts as the general in Algorithm OM(m-1) to send the value vi to each of the n-2 other lieutenants.
For each i, and each j  i, let vi be the value lieutenant i received from lieutenant j in step 2 (using Algorithm (m-1)). Lieutenant i uses the value majority(v1, v2,...vn).
Lamport's Algorithm Definition
To most programmers, this is going to look like a conventional recursive function definition. However, it doesn't quite fit into the conventional recursive function mold you learned when studying the example of factorial(n).

Lamport's algorithm actually works in two stages. In the first step, the processes iterate through m+1 rounds of messages. In the second stage of the algorithm, each process takes all the information it has been given and uses it to come up with its decision.


