# 401K Stable Value Protection Model

A fund model is presented for several contract subtypes involving stable value protection on 401K tax-sheltered investment plans. The model does not perform pricing, but rather makes an estimate of expected losses.  The estimation of fees received in compensation for extending the value protection is not treated in the model, although the loss estimation appears to be consistent with the detailed structure of these contracts.  

Extending the model to estimate fees is straightforward, and would also simplify certain other functions associated with booking and marking to market 401K wrap contracts, such as estimation of fee loss reserves.

The stable value model is aimed at pricing the value of providing value protection on a portfolio consisting of fixed income instruments.  The protection provided by the stable value contract is written on any shortfall between the market value of the fund and a defined “book value” which exists when redemptions or withdrawals from the fund are made.

The book value of the fund is computed as a function of the “crediting rate”, which itself is a function of previous market and book values as well as the equity and/or fixed income indices that indicate the market value of the fund.

The yield on the fixed income component of the protected fund is modeled as the change through time of a single rate which is considered to be the value of a fixed income yield index.  The model used is a single mean-reverting interest rate process given by

  (1)

where  ,  is the mean reversion speed,   is the log of the long-run interest rate,   is the volatility of  .  The parameters must be estimated by running a regression on historical data for the yield index in question.

The book value of the fund is a function of the crediting rate  , which is given by

  (2)

where   is the market value,   is the book value,   is the fixed income yield from equation (1) and is the duration.

The need to discount the future payouts to present values makes it necessary to model the risk-free discount curve.  The model currently does this using a constant curve of discount factors (see https://finpricing.com/lib/IrCurve.html).

The market value is computed according to

  (3)

where   is the fixed income index return.  The protected portfolio has a duration which is either constant or declining, depending on the type of fund being modeled. Thus the return on the fixed income portion of the portfolio can be written

  (4)

where   is the simulation time step or portfolio rollover time, and   is the duration at time  .

Modeling of 401K stable value contracts, commonly called “wraps”, is complicated by the structural features of these contracts and of the funds to which they are applied.  401K plans typically grow and shrink as fund participants make deposits and  make withdrawals.  The deposits and withdrawals of the participants are modeled as a normal random variable, that is 

  (5)

where   is the growth rate per period   at time  ,   is the mean annual growth rate, and   is the annual growth rate volatility.  

To meet the regular fund “turnover” the fund management typically holds a certain percentage of the fund as cash and cash equivalent assets.  All withdrawal activity is first met with this cash buffer.  If the cash buffer is insufficient, then assets in the fund are liquidated according to a schedule which depends upon the fund/contract structure.

Two basic types of contract structure exist, LIFO (last-in, first-out) and pro-rata.  LIFO refers to the order of liquidation of the assets in the fund.  When a LIFO contract is entered into, it is typically written on the most recently purchased asset in the fund.  This asset has a fixed maturity and is held until it matures, or until liquidation is required.  When the asset matures, or when the entire face value of the asset has been liquidated to meet fund withdrawals, the stable value contract expires.

Since under a LIFO contract we are wrapping a particular asset, and this asset has a specific maturity date, we must take into account the reduction in duration, and thus yield sensitivity, of the asset over time.  Therefore, for a LIFO wrap, the market value is given by equation (3), but with the duration reduced by the amount 

  (6)

in each time step since we are not actively managing a constant-duration portfolio, but instead holding an asset to maturity.  In the case of pro-rata wraps, we hold the duration constant since our pro-rata slice of the portfolio is being actively managed.

In addition, as other assets held in the fund mature and new assets are purchased, these newly purchased assets replace previously purchased assets as the “last-in” assets.  Thus as assets mature and are replaced with new assets, the new assets have higher priority for liquidation.  As assets in the fund mature, therefore, the size of the withdrawals necessary to trigger liquidation of the wrapped asset grow rapidly.  It should be remembered, however, that in cases where the cash buffer is below its “target” value, the cash flows from maturity assets are first used to top up the cash account.

The amount of the fund which matures in each modeling period is given by

  (7)

where   is the LIFO-Maturing amount,   is the fund size,   is the wrap contract notional,   is the average life of the fund, and   is the length of a modeling period.  The notional amount of the wrap contract is not considered to be part of the fund for the purposes of calculating  .   Note that the IPS model instead uses the slightly modified formula

  (8)

which effectively assumes that the wrap contract of interest represents the entire amount of the fund which will mature at the specified maturity date, thus all assets in the fund other than those wrapped will mature on or before   years.

The second 401K contract type is the pro-rata.  As the name would imply, the basic structure of this contract is that it covers a fraction of the fund size.  Thus, as the fund grows or shrinks, the effective notional size of the contract changes.  However, other wrap contracts with payout priority may exist within the fund, in which case any growth in the fund is assumed to increase the amount of the fund which must be liquidated before liquidations of the wrap of interest occur.  Thus the effective notional is held constant, as is the duration, since we are wrapping a portion of an actively managed portfolio rather than a single asset.  Otherwise, no change in priority of liquidation occurs, and the writer of the wrap is responsible for their pro-rata share of the payouts to be made under the contract.

The purpose of the wrap is to make up any shortfall between the book value, as defined by the crediting rate, and the market value of the asset at time of liquidation.  The crediting  rate is affected by the relationship between market and book value, but any payments made by the wrap  may also be affected by what is termed participation.

Participation refers to the fact that the fund holders may “participate” in the gains and losses in the market value relative to the book value of the fund.  Consider, for example, that withdrawals by a substantial fraction of the fund participants triggers the necessity to liquidate a certain portion of a wrapped asset.  Withdrawals from the fund are always at book value, and let us assume for the purposes of illustration that when the withdrawals are made, the market value is below the book value.  If the liquidation is participating, then an amount of the asset sufficient to cover the book value of the withdrawal is sold at market value.  The crediting rate of the fund is then recalculated to reflect the fact that an the market value has been reduced by an amount equal to the book value.  The reduction in the crediting rate implies a reduction in future book value, and thus the fund holders are said to “participate” in the losses of the fund.  Of course, in the case that the market value is above the book value, the fund holders also participate in the gains through an increase in the crediting rate.  

If the fund is non-participating, then under liquidation, a notional amount of the asset which corresponds to the book value is liquidated by the fund management, and the wrap counterparty is then required to make up any shortfall between the market value at the time of liquidation and the book value.

A hybrid fund is partially participating.  What this indicates is that the fund unit holders participate in losses up to some predetermined maximum level.  Redemptions of the fund beyond this value are treated as non-participating.  The participation level is expressed as a percentage of the fund notional, and is usually calculated as a running sum over a defined time period, typically annually.  Thus any redemptions which do not occur within the time period do not count toward the participation limit.  These limit rules on participation thus render payouts under the wrap contract less likely.

Another possible feature of  401K wraps is known as a managed maturity component.  This usually refers to a separate portion of the fund in the form of a sub-portfolio.  This sub portfolio is considered to have a particular relationship to the wrap contract of interest.  At this time the test model has only a very simple managed maturity facility, which simply adds the amounts which are maturing in the sub-portfolio to the cash buffer if necessary.  Amounts flowing into the cash buffer are deducted from the base fund, while amounts in excess of the cash buffer are added to the protection layer in the case of a LIFO wrap, and for pro-rata wraps when there exists a higher-priority tier in the structure.  The cash flows from any managed maturity component also reduce the likelihood of payouts since the cash buffer has a larger influx of liquidity.
