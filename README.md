# Kalshi Calibration Study

A prediction market is calibrated if its prices match how often things happen: a contract at seventy cents
should pay out about seventy percent of the time. Unlike almost any other claim in finance, you can check
this one directly, because every contract eventually settles to a known answer.

I built a bot to check it at the confident end of the price range, where the literature says the error
should be, and to find out whether anything survives the cost of trading on it. Something showed up, and a
month of trading cannot prove it. Working out why I could not prove it turned out to be the real project.

The code and the research pipeline are private. This is the write-up.

## The bias

Long shots are overbet and favorites are underbet, so betting odds give biased estimates of what actually
happens (Snowberg and Wolfers, 2010). It was found first at the racetrack, where Thaler and Ziemba (1988)
reviewed it alongside the other pricing anomalies in parimutuel and lottery markets, and it has kept
showing up in sports books and on betting exchanges since. It is one of the most replicated results in
betting markets.

Why it happens is less settled. The neoclassical story is that gamblers knowingly overbet long shots
because they like risk; the behavioral one is that they misjudge small probabilities. Snowberg and Wolfers
(2010) tested the two against each other on a large racetrack dataset and found for misperception, which
fits prospect theory. Ottaviani and Sorensen (2008) survey the wider set of explanations.

For my purposes the cause does not matter. If the bias holds on a regulated exchange, favorites are cheap
and buying them should make money. Whether it actually does comes down to costs.

## Why costs decide it

Three things stand between a mispriced contract and money in your account.

The spread is the gap between the buy and sell price. The edge on a confident contract is small in
absolute terms, so the spread takes a large share of whatever is there. Fees are charged on every trade
and are structured to cost most on contracts priced near even odds. Liquidity sets the last limit: an
order only fills if someone takes the other side, which caps your size no matter how much capital sits
behind it.

None of the three is large alone. Together they are enough to erase a bias that looks obvious in the price
history, which is why a study that stops at "the prices are wrong" has not finished.

## What I built

A bot that runs one fixed set of rules with real money, logging every decision, order, fill and settlement
it makes.

A backtest where fills are capped by what the market could really absorb. This was the decision that
mattered most. Kalshi publishes a record of the trades that actually happened, so I limited every
simulated order to the volume genuinely available at that moment. Assume instead that orders fill in full
and the same trades report 710 percent rather than 135 percent, which is 5.3 times the profit from
identical decisions. That gap is the difference between a result and an artifact.

The study runs on about 3.6 GB of market data covering 254,208 settled contracts over roughly eleven
months: every one of these instruments Kalshi has listed since launch, priced minute by minute, plus the
public trade record and the bot's own logs.

## What I found

An edge showed up. A month of live trading across several thousand positions, with real fills and real
fees, came out positive, and the backtest over the longer history agrees.

It is not proven. Every estimate from a finite sample carries a range, because the same month could have
gone differently on luck alone, and my range still includes zero. A rule with no edge at all could have
produced the same record. That does not mean the rule is broken; it means a month is not enough to tell.

The limit is structural rather than impatience. These contracts are new, and some categories are months
old rather than years, so there is no deep history to check against and no way to extend the live record
backward. The data arrives only as fast as the exchange makes it.

The ride is not smooth either. The worst peak-to-trough fall in the reconstruction was 26 percent of the
account. An edge this thin comes with stretches long enough to make you doubt it, which is most of why I
write the pass mark down before I look at the answer.

## What I got wrong

Three mistakes cost me real time, and all three made the results look better than they were.

I treated correlated positions as independent. When an asset moves, every contract tracking it moves too,
so counting those separately inflates your sample badly. Grouping them by the moment they opened roughly
doubled every confidence range I had, which is another way of saying it halved what I actually knew.

I scored rules on the data I tuned them on. Settings picked because they worked over a stretch of history
will work over that stretch; the only clean number is money committed after the parameters are frozen.
Several ideas looked strong in a backtest and were worth nothing on data they had not seen.

I assumed profit scales with capital. Position size grows with the account but liquidity does not, so past
a fairly small account the extra size simply does not fill. Five times the capital returned 2.7 times the
profit, and the return on my own money fell by about half. That caps the approach whether or not the edge
is real.

## Most of it failed

The failures are the part worth reporting, because catching them is what the method is for.

I tested eight more elaborate entry rules against the simple one and none of them beat it. I tested
sixteen other contract series for the same effect and none showed an edge that survived a second look.
Sizing positions by how confident the price was lost to sizing them flat. Buying the other side of the bad
cases lost in every price range. An exit rule for correlated positions lost to holding them. Ideas drawn
from entry timing, from opening-price behavior, from a second venue and from a different asset class were
all tested and all dropped.

Counting distinct ideas rather than variants, roughly twenty were tested properly and the large majority
were abandoned.

## What the project is worth

Not the return number. The useful output is a way of testing a small effect that does not quietly answer
yes: a pass mark written down before the result is read, a held-out period scored exactly once,
uncertainty computed across correlated groups instead of individual bets, fills capped by volume that
actually traded, and a written record of every idea that failed, which is the longest file in the project.

The study is still running. As these markets age they will build the history they do not have yet, and the
question will become answerable in a way it is not now.

Finding something was the easy half. The work was building a test that could tell me I had not.

## Not included

No entry or exit rules, price thresholds, timing, filters, position sizing, risk limits, instrument
selection, account balances, or anything about the infrastructure.

## References

Ottaviani, M. and Sorensen, P. N. (2008). The Favorite-Longshot Bias: An Overview of the Main
Explanations. In Hausch, D. B. and Ziemba, W. T. (eds), *Handbook of Sports and Lottery Markets*,
pp. 83-101.

Snowberg, E. and Wolfers, J. (2010). Explaining the Favorite-Long Shot Bias: Is it Risk-Love or
Misperceptions? *Journal of Political Economy*, 118(4), pp. 723-746.

Thaler, R. H. and Ziemba, W. T. (1988). Anomalies: Parimutuel Betting Markets: Racetracks and Lotteries.
*Journal of Economic Perspectives*, 2(2), pp. 161-174.

## License

MIT
