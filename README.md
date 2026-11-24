Machine-maintenance-markov_Chains


Modelling machine availability with a Markov chain

The question

A production machine is not just simply on or off, as it wears gradually, sometimes fails and then has to wait for and go through repair which means the share of time it is actually available to produce isn't obvious from any single shift.

This project models that process as a discrete-time Markov chain to ask two questions: what share of the time is the machine available in the long run, and what does servicing machines once they start to wear actually improve that compared with waiting for them to break?

The model

Each shift, the machine is in one of four states: Working, Degraded (still running but deteriorated), Broken, or Under Repair. I set each diagonal entry of the transition matrix from an assumed average duration in that state, since time spent in a state is geometrically distributed, so an average stay of d shifts corresponds to a staying probability of p = 1 − 1/d.

Under the preventive policy:

State	Average stay	Then moves to	What it represents

| State    | Average stay | Then moves to                       | What it represents                                              |
|----------|--------------|-------------------------------------|-----------------------------------------------------------------|
| Working  | ~20 shifts   | always Degraded                     | Machines wear gradually rather than failing without warning     |
| Degraded | ~1.4 shifts  | Repair 6 in 7 times, Broken 1 in 7  | Wear is usually spotted and serviced before it becomes a failure |
| Broken   | ~1.1 shifts  | always Repair                       | A technician starts almost immediately                          |
| Repair   | ~1.1 shifts  | always Working                      | Routine servicing is quick and always succeeds                  |

Under the reactive policy, nobody services degraded machines, so every episode of wear ends in a breakdown, and I assumed breakdown repairs take longer (around 2 shifts), as they are typically bigger jobs than routine servicing.

Results

Solving πP = π gives the long-run share of time in each state:

| Policy     | Working | Degraded | Broken | Repair |
|------------|---------|----------|--------|--------|
| Preventive | 88.1%   | 6.3%     | 0.7%   | 4.9%   |
| Reactive   | 81.5%   | 5.8%     | 4.5%   | 8.2%   |

I also simulated 10,000 shifts under the preventive policy and tracked the running share of time in each state. Each line settles onto its theoretical value, which is the ergodic theorem in practice and shows that the simulation and algebra agree.

What It Shows:

Preventive maintenance raises availability by about 6.6 percentage points, and the reason is not that machines last longer between problems, since in both cases a machine works for around 20 shifts before wear sets in.

The difference lies entirely in what happens next: under the preventive policy six in seven episodes of wear are caught and fixed with a quick service, which costs around 2.7 shifts of downtime per episode, whereas under the reactive policy every episode ends in a breakdown followed by a longer repair, costing around 4.5 shifts.

The result also holds if breakdown repairs are assumed to take no longer than servicing, with availability under the reactive policy still lower at around 84.6%, because every breakdown adds a wait before repair can begin, which suggests the conclusion is reasonably robust to that assumption.

Limitations

The model assumes the Markov property, which means the machine has no memory, whereas real machines age and become more likely to fail over time, which an age-dependent or semi-Markov model would capture better.

The transition probabilities are assumed rather than estimated, so the natural next step would be to fit them to real maintenance logs.

Degraded shifts are counted as unavailable, although in practice a degraded machine may still produce at a reduced rate.

Availability ignores cost, and preventive servicing uses technician time and parts, so a full comparison would attach a cost to each state and compare the long-run cost per shift, π · c, under each policy.

Rare states converge slowly in simulation, as breakdowns are exactly the rare events that are hardest to estimate from limited data.

Next steps

Adding a cost to each state to compare policies on cost per shift rather than availability alone, and finding the point at which preventive servicing stops being worth it, for example how expensive it would have to be before the reactive policy becomes cheaper.
