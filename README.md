# Defining-Idiosyncratic-Volatility-with-Deutsche-Boerse-A7
_Canari.dev (www.canari.dev), April 2021_

What part of the equity options' implicit volatility movement can be explained by the underlying spot moves and the global market ?

**This project is based on Deutsche Börse's A7 service which provides intraday prices and visualization tools for all Xetra and Eurex instruments.<br>
You will need a valid A7 subscription to run it. <br>
For this, please go to : https://www.mds.deutsche-boerse.com/mds-en/analytics/A7-analytics-platform**

This git also uses results from our earlier project : https://github.com/canari-dev/Calibrating-implicit-volatility-surface-with-Deutsche-Boerse-A7 which shows how to get implicit volatility and forwards from options prices.<br>
You will first need to run this preliminary git to generate the volatility time series that we use in this project.


# So, what is "Idiosyncratic Volatility"? # 

Canari.dev runs machine learning algorithms on market related time series in order to predict parameters move.
When focusing on single stock options' implicit volatility, Canari.dev focuses on the part of the movement of the vol which cannot be explained by the underlying/Eurstoxx50 movement. That's what we call the idiosyncratic volatility.

There are two reasons for that :

1/ The more a parameter is specific and hard to trade (like a single stock implicit volatility), the more likely one is to find strong predictive signals on it because its price is less driven by arbitragers.
That's why the idiosyncratic vol is where we can look for strong signals.

The SX5E volatility and the underlying stock of an option are very easy to trade and subject to intensive arbitrages, so coming up with forecast signal on these requires very different techniques and should be seen as a separate endeavour.

This is the reason why, prior to running Machine Learnin algo, we split the volatility moves into market_move and indiosyncratic_move.
This greatly improves ML efficiency.

2/ An option trader would rightly argue that the performance of his investment in options is determined by the "whole volatility moves", not not just the idiosyncratic part.

Two possible answers : 

- Either to neutralize the impact (ie. hedge) of underlying / Eurostoxx50 moves with cheap (in terms of bid-offer spread) hedging in stocks and SX5E options, 
Or
- Suplement the idiosyncratic vol estimator with others (necessarily weaker) signals, focused specifically on stock moves and global (ie. SX5E) volatility. Those signals would then be added to the idiosyncratic vol after mutliplication by the relevant sensitivity (respectively vol/spot sensitivity and vol/volSX5E sensitivity)



-------------------------------------------------------------------------------------------
## Now let's see what we really mean by idiosyncratic vol  and what are its caracteristics  :

The Jupyter Notebook provided here shows how to run a regression between the volatility moves and explanatory factors.
The idiosyncratic vol moves is the vol moves minus the part that can be explained by a simple regression on hedgeable parameters.
We will focus on 3 instances :

# 1/ Regressing single stock volatility moves on simulatneous underlying spot moves (*)

(This idea is linked to the measure of realized smile)

Table : R2 factor of the regression ie. how much of the variance of Y can be explained by the regressors
![plot](./images/results1.png)


Remarks :
The fixed strike vol, with only 13% of R2 is very close to the idiosyncratic vol (which would have 0% R2 by definition).
It is better than the ATM vol whose variation are very much correlated to spot moves.


# 2/ Adding the Eurostoxx50 spot and vol as regressors (*)

Table : It is now a multiregression with 3 Xs : Underlying spot move, SX5E vol move, SX5E spot move
![plot](./images/results2.png)


Remarks :
First we can see that SX5E (a proxy for global markets) explains c. 15% of variance on top of what the underlying's moves explain (or 1/3 of the remaining variance).
Also, it is interesting to see that the R2 is significantly higher for lag of 1 day than for 1 hour.
This shows that over a few hours, the single stock vol can depart from it's mean spread with SX5E vol, but will likely mean revert in a matter of 1 or 2 days.
This hints at a potential mean reverting indicator...


# 3/ Now having a look at the Eurostoxx50 vol its underlying movement

(mono regression of the vol moves on spot moves)

Table : It is now a multiregression with 3 Xs : Underlying spot move, SX5E vol move, SX5E spot move
![plot](./images/results3.png)


Remarks :
The SX5E's vol is much more linked to the spot of its underlying than single stock vols.
There is little room for idiosyncratic vol here, as the vol is better arbitraged.





# (*) Context : 
R^2 of an OLS regression on maturities between 2 and 12 months.
All moves capped at 3 standard deviations
Period from April 2nd, 2019 to january the 31st, 2020.
- ATM vol is the at the money vol (Strike = Forward)
- Fixed strike vol is the vol of a strike fixed on the forward at the beginning of the period
