# Roman Anthony vs Juan Soto
## Quotes / Motivation

Over the summer, Roman Anthony debuted for the Red Sox, and was immediately impactful, which resulted in lots of buzz. Some of the responses to his play stuck with me for a while because I wasn't sure if I believed the conclusions these articles came to from just a few statistics. 

"Who he reminds me of, it's hard," Cora said, per The Athletic's Ken Rosenthal. "I don't want to say (Barry) Bonds, of course. Probably (Juan) Soto, without the flashiness, early on. It's a good at-bat. He's not going to chase. Even when he doesn't get hits, you're like, holy s---, that's a good at-bat" (SOURCE)

"Anthony is averaging 4.25 pitches per plate appearance," Rosenthal wrote. "His walk rate is 14.6 percent. In those categories ... he would rank among the league leaders if he had enough playing time." 
(SOURCE)

"Players to rank in 95th percentile in hard-hit percentage and chase percentage in 2025, there's two of them. It's Juan Soto and Roman Anthony," Mark DeRosa shared on "MLB Central." (SOURCE)

It is easy to mislead people with statistics. I don't think the above people are attempting to mislead anyone, but comparing anyone to Juan Soto is a big deal! That changes a team! (Despite the 2025 Mets, unfortunately). Anthony is about my age, and despite the data not supporting it, I instinctually am fascinated by high walk rates in younger players (according to statcast, the relationship between age and bb% has an r2 of 0.01). Also, a recent mlb.com article listed a lot of these statistics as reasons the Red Sox should be optimistic about next year, and I am nothing if not a pessimist about the Yankees' chances in the AL East, but I'd like to be a realistic fan. If Anthony is actually a game-changing player like Soto, I'd like to regulate my expectations about the upcoming season.

So, let's take a look! Do those two statistics actually represent their style during a plate appearance? Is Roman Anthony the next Juan Soto?
### Planning
Before starting any analysis, it is important to make sure you actually know how to answer the question you are asking. For me, that is 
> Does Roman Anthony's plate approach at the MLB level mirror that of Juan Soto?

In order to answer this, I had to choose whether or not I wanted to compare 2025 Soto to 2025 Anthony (which would make direct numeric comparisons easier) or comparing rookie Soto to rookie Anthony, i.e. 2018 Soto to 2025 Anthony. I decided to look at both, with an emphasis on their rookie seasons, because I want the end goal of this analysis to be how Anthony might grow as a player, not just how they currently line up. Also, Cora specifically compares him to "early" Juan Soto. So, the question became:
> Does Roman Anthony's plate approach in 2025, at the MLB level, mirror that of Juan Soto in 2018?

To answer this question, we have to specify what a player's approach is. This is an exceptionally complicated question, so I narrowed once again, and considered their decision to swing, given a certain pitch location and type, and the quality of contact or lack thereof. Unfortunately, we can't look at squared-up rate before 2023 because bat speed was not tracked, so I had to consider quality of contact differently. Recall, however, that we are interested in approach rather than results, specifically whether a given player has a "good at-bat" (Cora, quoted by Rosenthal). So, we examine swing%, chase%, whiff%, and contact%, and since DeRosa cited HardHit%, we will also look at exit velocity. We now are trying to answer: 
> Does 2025 MLB Roman Anthony swing, chase, whiff, and make contact at the same rate as 2018 MLB Juan Soto after controlling for pitch location and type, and normalizing across seasons?

I am specifically interested in swing percentage because DeRosa mentioned chase percentage in his tweet, but chase percentage is a subset of swing percentage. It is somewhat misleading to say that Anthony has a low chase percentage because he has a good eye for the zone without noting that he also has a low swing percentage. 

## Methods
First, instead of running a power analysis (to make sure I had enough data, because Anthony only played a little over half the season), I found a [stabilization analysis](https://web.archive.org/web/20080102094412/http:/mvn.com/mlb-stats/2007/11/14/525600-minutes-how-do-you-measure-a-player-in-a-year/) through FanGraphs which found that both swing% and whiff/contact percentages stabilized around 40PA. This meant I had enough data to begin. I downloaded all of the pitches from 2018 and 2025 from StatCast via pybaseball (baseballr was acting up). 

I decided to do my analysis in R rather than Python (which I have more experience with) because I both wanted to get more practice with R and because it has a very straightforward library for [Generalized Additive Models](https://m-clark.github.io/generalized-additive-models/case_for_gam.html) (GAMs) which essentially piece together a bunch of possibly non-linear relationships over a given space.  This works better than linear and polynomial models, even when piecewise, because it is smooth and allows for different relationships over the surface, while ensuring the resulting model is still consistent. I also used the BAM model from the mgcv library in R because it is a modified GAM intended for larger datasets, and there are a lot of pitches thrown in the MLB over a season.
<details>
<summary>If you want more information about the models, click here! </summary>

If you have taken linear algebra, you can think of the model fitting as choosing all of our partial models from the same basis, i.e. a minimal generating set. For example, all of the formulas are some form of cubic equation, but they do not have to be the same one over the whole space. Then, we piece them together for a smooth result. The link above provides an example of a polynomial spline, which looks at each section of the data independently, then (intuitively, or, as my Mathematical Logic professor would say, morally) slides them up or down to 'match' the end points. The GAM is more complicated than this due to penalized regression (which I do not know how to do yet). 
</details>

I also ran this model in two ways: (1) recognizing the pitcher and game as an effect on each pitch, which could overfit the model, and (2) not considering each pitcher/game on the resulting decision, which could prioritize certain pitchers/games for the resulting predictions, based on sample size. I decided against making a model specifically for whiffs, because contact predicated on swings is just the negative, and the heatmaps were more visually coherent.






NEXT STEP omnibus test for pattern

TEST: <table>
  <caption>Difference-in-Differences Results by Zone and Metric</caption>
  <thead>
    <tr>
      <th>Zone</th>
      <th>Metric</th>
      <th>DiD Estimate</th>
      <th>95% CI</th>
      <th>% Significant Area</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Heart</td>
      <td>Contact Probability</td>
      <td><b style="color:black;">-0.019 ●</b></td>
      <td>[-0.023, -0.016]</td>
      <td>25.3%</td>
    </tr>
    <tr>
      <td>Shadow</td>
      <td>Contact Probability</td>
      <td><b style="color:black;">-0.026 ●</b></td>
      <td>[-0.034, -0.017]</td>
      <td>53.2%</td>
    </tr>
    <tr>
      <td>Waste</td>
      <td>Contact Probability</td>
      <td><b style="color:black;">0.033 ●</b></td>
      <td>[0.029, 0.037]</td>
      <td>23.7%</td>
    </tr>
    <tr>
      <td>Heart</td>
      <td>Exit Velocity</td>
      <td><b style="color:black;">-0.709 ●</b></td>
      <td>[-0.796, -0.622]</td>
      <td>0.0%</td>
    </tr>
    <tr>
      <td>Shadow</td>
      <td>Exit Velocity</td>
      <td><b style="color:black;">0.420 ●</b></td>
      <td>[0.336, 0.504]</td>
      <td>0.0%</td>
    </tr>
    <tr>
      <td>Waste</td>
      <td>Exit Velocity</td>
      <td><b style="color:black;">4.124 ●</b></td>
      <td>[4.029, 4.219]</td>
      <td>0.0%</td>
    </tr>
    <tr>
      <td>Heart</td>
      <td>Swing Probability</td>
      <td><b style="color:black;">-0.074 ●</b></td>
      <td>[-0.075, -0.073]</td>
      <td>93.8%</td>
    </tr>
    <tr>
      <td>Shadow</td>
      <td>Swing Probability</td>
      <td><b style="color:black;">-0.045 ●</b></td>
      <td>[-0.047, -0.043]</td>
      <td>22.8%</td>
    </tr>
    <tr>
      <td>Waste</td>
      <td>Swing Probability</td>
      <td><b style="color:black;">0.009 ●</b></td>
      <td>[0.009, 0.010]</td>
      <td>8.1%</td>
    </tr>
  </tbody>
</table>




