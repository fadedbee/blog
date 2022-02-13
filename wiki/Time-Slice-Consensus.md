# Time Slice Consensus

TSC has a leader for every second.

Applicants and Peers are identified by tuples of `(IPv4, PublicKey)`.

All peers know the approximate set of current peers.

The leader for a particular second is deterministically calculated from the set of peers and the epoch time (of the second).


# Availability

Availability is provided by each second having a different leader.

If a leader does not perform, it is eventually removed from the set of peers.


# Leadership

The leader (for any particular second) is calculated using Consistent Hashing.

Peers form a circle of circumference 2^32.  A peers position is just its IPv4 address.

The leader is the peer clockwise from position `hash(epoch_time)`.

Applicants will not be promoted to peers if their IPv4 address would make them a leader in the next N seconds.  This creates stability.



