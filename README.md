# HAPPI
model description and code
HAPPI - Model documentation

In this document, we present the HAPPI model (Heterogenous Agent-based Power Plant Investments model), see Yang et al. (2021), following the ODD protocol (Overview, Design Concepts, and Details) (Grimm et al., 2006; Grimm et al., 2010; Grimm et al., 2020). The ODD protocol aims to provide a standardised way of describing simulation models, such as autonomous individual organisms (IBM) or agent-based models (ABM), and make the models easier to understand and replicate (Grimm et al., 2006). We describe our model by going through all seven elements in the protocol.

	Purpose
The overall purpose of the model is to study the transition of the electricity sector to a low-carbon system. Specifically, we focus on the decisions making criteria of the agents, who are power companies and make investments in new generation capacities. We analyse how the electricity system develops over time under climate policy.

	Entities, state, variables, scales
The model includes two types of entities. The first one is the power company. They are characterised by two attributes, the hurdle rate value each agent uses (denoted by r in the model), and the expectation of future carbon tax each agent has (denoted by b in the model). A hurdle rate is the minimum rate of return on an investment required by a company, which indicates a company’s risk management. The higher r a company employs (for evaluating an investment option), the higher risk the investor perceives. The variable b indicates the increasing rate of the carbon tax that one company believes. The higher b value one company has, the faster the increase one company thinks.

The second entity is the power plant. There are six types of power plants in the model:  coal-fired, gas-fired, gas-fired with carbon capture and storage (gasCCS), nuclear, solar PV, and wind power plants. The plants are characterised by six attributes-the fuel type, investment cost, size, carbon emission intensity, fuel cost, and lifespan.
The temporal resolution of the model is one year, and each year is further divided into 64 slices (detailed information about the slice is in the “Input data” section). We run the model for 80 years in total.

	Process overview, scheduling
In each time step, we simulate two types of decisions that each agent makes. First, the individual company makes decisions about electricity production. Companies produce electricity from power plants that have lower (or equal) operation costs than the electricity price. The electricity price is calculated based on an iso-elastic demand function.

The second decision is the investment decision. Each year, plants reach the end of their lifespan get decommissioned, and agents take turns to make investment decisions on new generation capacities, based on expected investment profits. After all companies finish making their investments, the model goes to next year.

State variables are updated both on the individual company’s level and on the system level. One the company’s level, the individual installed capacity and financial performance (money in the bank account, and equity) are updated. If a company’s equity goes below zero, then it goes bankrupt and is not allowed to make further investments. On the system level, aggregated installed capacity is updated annually. We also make an ex-post calculation of the Internal Rate Return (IRR) of each investment, averaged annual electricity price, electricity production profile, and emission trajectories.

	Design concept
Basic principles
The electricity system transits to a low-carbon system under a growing carbon tax scenario. 

Emergence
The development of the capacity mix on the system level emerges as a result of individual companies’ new investments and the exiting of obsolete plants. (The transition of the electricity system occurs through this emergence.)

Adaptation
In the current version, there is no adaptation behaviour, but we plan to use adaptive hurdle rates in the future version of the model, which means companies adapt their hurdle rates based on the profits of their previous investments.

Objectives
The objective of each company is to maximise its profit for each investment. Companies rank the investment options by expected profitability and choose the one with the highest expected profitability. The profitability is based on a Net Present Value (NPV) calculation.

Learning
In the current version, no learning process is included. 

Prediction
Companies make two types of predictions. First, they predict future carbon prices. Companies have limited information about future carbon prices. They look n years ahead and predict how fast the tax will grow. Their expectations are given by a growth factor b times the true tax in n years (details see in the submodel section). 
Secondly, taking into account the future carbon tax, agents make predictions about the profitability of potential investment options.

Sensing
Two types of sensing are included in the model. First, companies have information about the carbon tax for next year. Second, once a company has made an investment decision, information about the type (and size) of the plant will be made public immediately.

Interaction
There is no direct interaction among agents/companies. However, the agents interact with each other indirectly via competitions. The agents compete with one another in the electricity market by selling electricity and investing in new power plants. If an agent performs badly (equity goes below zero), then it goes bankrupt and is not allowed to make further investments, while agents who perform well will accumulate profits over time and take more market share.

Stochasticity
Three stochastic processes are used in the model. First, agents take turns making investment decisions, and the sequence of agents is randomly assigned each year. The second stochastic process is the development of fuel prices over time (coal and gas prices). Lastly, the model has implemented a stochastic electricity demand as well. 
The second and third stochastic processes can be turned on or off depends on the case that we investigate. 

Collectives
The capacity mix on the system level is an aggregation of individual agents’ investment decisions, while the capacity mix of the system in turn, also affects individual agents’ new investment decisions via affecting the revenue received by different types of investment choices.

Observation
Data is collected both at the individual agents' level and the system level. At the agent level, we collect information on each agent’s investment decisions, return of its investments, money in its bank account, and equity. While at the system level, we collect the capacity mix, electricity production profile, emission trajectory, and averaged annual electricity price. All data are displayed as outputs of the model and are illustrated by plots.

	Initialisation
Power plant
The model starts at year 0 with a stationary state with 64 GW coal and 2 GW gas in the system. Each coal and gas plant has a capacity of 500MW. The total size of the initial capacity is comparable with the electricity system in a country like Germany.
We separate these initial coal and gas plants and placed them in an additional company that does not take part in the investment process, but only runs the plants throughout their lifetime.

Companies/Agents
The initial number of companies varies depends on the design of the company’s two attributes (the hurdle rate r and the expectation of future carbon tax b). The initial number of the company equals all possible combinations of set r and set b. 
Example:
	r = [4.5%,5%,6%,8%] per year.
	b = [0,0.5,1.0,1.5,2.0] .
There are 20 combinations of r and b, plus one additional company that owns the initial plants, but does not participate in make investment, therefore, there are 21 companies in total.

Stochastic process
The stochastic process of the fuel price (gas and coal prices) and the electricity demand can be set to “off” or “on”. The stochastic process is off if the parameter value is set to 0, and it is on if the value is larger than 0. (See section 7 for details)

Financial module
Several parameters regarding the financial settings need to be initialised.
	f : the investment fraction paid by a company’s capital. 
	f_div  : dividend fraction.
	i: initial capital in a company’s bank account.
	m_save:  a company’s reserved amount after the dividend is paid.

Example:
	f=30%.
	f_div=50%.
	c_initial=400 M€.
	m_save=1500 M€.
See section 7 for more details about the financial module.

	Input data 
(These are time-series data used to ‘drive’ the model)
The model uses several external input data. First, the tax scenario is given externally. In our base case, the carbon tax is 0 €/ton CO2 in the first 10 years, and then it increases by 2€/ton per year and reaches at 100 €/ton at year 60, and it stays at 100 €/ton afterwards.
Second, the electricity demand, wind availability, and solar availability are also external data. The detailed value is provided in the table in the Appendix.

	Submodels
In this section, we describe all processes listed in the “process overview and scheduling” section.

Electricity production and price
The companies produce electricity in an ideal electricity market. They produce electricity as long as the electricity price is larger than (or equals to) the running cost of their plants, following the merit order. The electricity price and the production quantity are determined by an iso-elastic demand function (Eq. 1)
█(q_τ  = q_(0,τ)× (p_τ/p_0 )^ϵ#(1) )
where p_τ is the electricity price in time slice τ, which is reached when the electricity produced (and consumed) q_τ meets the demand. The reference demand q_(0,τ)  is given and reflects the varying demand over different time slices in Germany in 2011. The reference electricity price p_0=3.25€ct/kWh, and the elasticity ε=-0.05 (Jonson et al., 2020). One year consists of time 64 slices with various hours (in Appendix Table A1).
The average electricity price figure presented in the main paper is a production-weighted average price.

The expectation of future tax prediction
Companies have limited information about future carbon prices. They know the true carbon tax of next year, and they estimate the tax level n-year ahead. Their expectations denoted F_b (t+n), is given by a factor b times the difference between the true tax in n-years, denoted by T(t+n), and the true tax of next year, denoted by T(t+1),
█(F_b (t+n)=T(t+1)+b(T(t+n)-T(t+1))   #(2) )
We use a set  [0, 0.5, 1.0, 1.5, 2.0] for b, which means agents’ expectations of the tax increase rate ranging from zero to two times as high as the actual increase will be. Agent assumes that tax stays the same as F_(t+n) afterwards.

Financial performance – the financial module
This module keeps track of each agent’s financial status, and this, in turn, is used to determine whether an agent can afford to make further investments.
We use  the following mechanism in our approach: First, an agent is assumed to pay a certain fraction f of new investment from its bank account, the remaining part of the investment, (1-f), is paid by a loan from the bank with an interest rate r_l. Second, the own bank account can accumulate profit from the company’s plant operations and a certain fraction f_div of the capital can be paid to the shareholders as dividend d, provided that the planned investments for coming years can be made, as described below. There is no interest on the own bank account, but if the money in the bank account would go below 0, it is regarded as an ordinary loan with interest r_l.
We add the following variables characterising the state of a company and the requirements for new investments:
	The value of a company is assumed to be the total value of its plants V plus the bank account holdings M minus the debt D to the bank. This is the equity E of the company. If the equity goes below 0, the company is bankrupt.
█(E = M +V – D #(3) )

	From one year to the next, the money in the bank account changes due to net revenues R_net (defined as revenues from selling electricity minus operating costs), investment costs I, interest costs C_(l,f) (t), repayment of loans L_(l,f) (t), and dividend d paid to shareholders,  
█(M(t+1)-M(t)=R_net (t)-f∙I(t)-C_(l,f) (t)-L_(l,f) (t)-d(t)#(4) )

Note that C_(l,f) and L_(l,f) (t) together is given by (1-f)A_l, where A_l is the sum of all annuitized costs of the standing plants, since the fraction (1-f) of investment is financed by a loan. Similarly, investment costs f∙I are a fraction of full investment costs, where I is the sum of full investment cost for plants in a specific year. A company is not allowed to invest if the money in the bank account goes below 0.

The dividend d is paid according to the following the rules:
	First, investments are made according to the company’s rules of operation.
	A given amount m_save is reserved to allow for a future (hypothetical) investment in the most expensive plant. This means that dividend is not paid in general and especially not in the initial years when the companies are in a growth phase.
	Up to a fraction f_div of the money in the bank account is paid to the shareholders as a dividend, but not more than that m_save is kept, i.e., d=Min(f_div M,M-m_save ). 

	The total value of a company's plants, V, changes due to new investments I and capital depreciation of plants, C_depr,
█(V(t+1)-V(t)=I(t)-C_depr (t) .#(5) )

Each plant has a value V_plant that initially is equal to its investment cost and from then on equals the remaining debt that one would have if one would (hypothetically) have borrowed for the full investment at an interest r_l and paid the annualised capital cost every year. The value V_plant (t_R ) of the plant with a remaining lifetime t_R can then be expressed as

█(V_plant (t_R )  =I_plant  (1-(1+r_l )^(-t_R ))/(1-(1+r_l )^(-t_L ) )  #(6))

where I_plant is the plant investment cost and t_L is the full lifetime.

	The debt D changes due to new loans taken and repayment of present loans L_(l,f). New loans are taken to cover (1-f) of investment costs, so the change in debt is then
█(D(t+1)-D(t)=(1-f)I(t)-L_(l,f) (t) .#(7) )
	By combining Eqs. (3,4,6,7), we get the change in equity
█(E(t+1)-E(t)=R_net (t)-C_(l,f) (t)-C_depr (t)-d(t).#(8) ) 
Note that, since we give a value of the plants according to Eq. 6, the capital depreciation equals the repayment of a hypothetical loan for full investment costs.


Investments decisions
There are six types of power plants that a company can choose to invest in, namely, coal-fired, gas-fired, gas-fired with carbon capture and storage (gasCCS), nuclear, solar PV, and wind power plants. The parameter setting of the plant is listed in Table A3 in Appendix.
Each year, plants that reach their end of lifespan are decommissioned, and companies invest in new capacity. The model removes one retiring plant at a time. Following a plant removal, the agents take turns (the order is random) to make investment decisions for new plants. The decision process follows the following steps. 
First, an agent adds a hypothetical power plant (testing all the six available technologies) to the existing capacity mix. Taking into account the expected CO2 tax in the following n years, i.e., n= 10 years (following the tax expectation described by Eq. 2), the agent calculates the electricity price (Eq. 3), production profile, and the expected annual net revenues R_net of this newly added plant for next year and year n. The capacity mix including the hypothetical plant is assumed to remain the same. Based on this, all future annual net revenues R_net are estimated by (i) an interpolation of R_net between years 1 and n and (ii) assumed constant R_net from year n and onwards. Lastly, after evaluating every available technology, the agent chooses the technology with the highest positive profitability index (Eq. 9 and Eq. 10) to invest, given the condition that this agent can provide the required payment fraction f from its own bank account. 	
█(profitability index=NPV/I×CRF#(9) )
█(NPV=∑_(t=1)^T▒〖(R_t-C_t)/(1+r)^t   -I〗#(10) )
█(CRF=r/(1-(1+r)^(-T) )#(11) )
where NPV is the net present value of all future revenues and costs, T is a plant’s lifespan, and R_t and C_t are the revenues and costs at year t. T plant’s investment cost is I, and r is the hurdle rate used by the agent. The capital recovery factor (CRF) is used to compensate for the effect of different power plants’ lifespans.

In the present version of the model, if the agent cannot afford the payment to invest in the top NPV-ranked power plant, then it will not invest in the current round. 

Stochastic processes
In the current model version, three parameters can change stochastically over time. They are the gas price, coal price, and electricity demand. A first-order autoregressive model (AR1) determines parameter value (Eq. 12).
█(x_(t+1)=x_t+ε_1 (x ̅-x_t )+ε_2∙z_(t+1)  #(12))
where x ̅ is the long-term average value of the parameter x_t. Parameters ε_1 and ε_2 are the characteristics of the stochasticity of the AR process. The stochasticity comes from  z_(t+1), a real-valued random number between -1 to +1.

Internal Rate Return
The internal rate of return (IRR) of an individual investment is calculated ex-post and IRR is the solution to the equation,
█(0=∑_(t=1)^(t_L)▒〖R_net/(1+IRR)^t -I_plant 〗  ,#(13))
Where R_net  is the net revenues in year t, and I_plant is the investment cost of the plant. 

Return on equity
We use the return on equity ROE given by net revenues R_net (revenues from selling electricity less operating costs) less interest C_(l,f) and depreciation costs C_depr divided by equity E,
█(ROE=(R_net-C_(l,f)-C_depr)/E#(14))
i.e., net profit divided by equity. In case there is no dividend paid, R_net-C_(l,f)-C_depr is the change in equity from one year to the next, see Eq. (8). ROE thus quantifies the equity growth (before the dividend is paid).
 
Appendix
A1. Time slicing
Table A1  Number of hours in each of the time slices (adapted from Jonson et al. 2020). 
		Low wind	Medium-low wind	Medium-high wind	High wind
Low solar	Low q0	28	146	65	25
	Medium-low q0	256	1027	751	283
	Medium-high q0	153	645	434	255
	High q0	16	63	70	73
					
Medium-low solar	Low q0	34	128	53	21
	Medium-low q0	104	398	213	72
	Medium-high q0	59	268	235	119
	High q0	10	34	33	46
					
Medium-high solar	Low q0	24	35	19	7
	Medium-low q0	124	227	163	81
	Medium-high q0	264	703	419	138
	High q0	17	52	89	53
					
High solar	Low q0	0	0	0	0
	Medium-low q0	23	15	15	1
	Medium-high q0	48	102	10	8
	High q0	1	3	2	0



A2. solar availability and wind availability
Table A2 Values of the demand reference, solar availability, and wind availability 
	Low level	Medium-low level	Medium-high level	High level
Demand reference q0 (GW)	37.3060	47.6100	63.2400	71.5555
Solar availability	0	0.0311	0.3439	0.6731
Wind availability	0.0689	0.1912	0.4282	0.7470


A3. Power plant parameters
Table A3 power plants parameters – costs and efficiencies
Plant type	Capacity (unit size)
(MW)	Running cost
(cent/kWh el)	Investment cost
(euro/kW)	Lifetime
(years)	Emission intensity
(gCO2/kWh electricity)
Coal	500	2.0	1500	40	1000
NGCC 	500	4.6  (natural gas)	900	30	432
		8.0 (biogas)			0
NGCC with CCS	500	5.8275 	1400	30	51
Nuclear	500	1.0	6000	40	0
Solar PV	500	0	800	25	0
Wind	500	0	1500	25	0



 
References
Grimm, V., Berger, U., Bastiansen, F., Eliassen, S., Ginot, V., Giske, J., Goss-Custard, J., Grand, T., Heinz, S.K., Huse, G., Huth, A., Jepsen, J.U., Jørgensen, C., Mooij, W.M., Müller, B., Pe’er, G., Piou, C., Railsback, S.F., Robbins, A.M., Robbins, M.M., Rossmanith, E., Rüger, N., Strand, E., Souissi, S., Stillman, R.A., Vabø, R., Visser, U., DeAngelis, D.L., 2006. A standard protocol for describing individual-based and agent-based models. Ecological Modelling 198, 115-126.

Grimm, V., Berger, U., DeAngelis, D.L., Polhill, J.G., Giske, J., Railsback, S.F., 2010. The ODD protocol: A review and first update. Ecological Modelling 221, 2760-2768.

Grimm, V., Railsback, S.F., Vincenot, C.E., Berger, U., Gallagher, C., DeAngelis, D.L., Edmonds, B., Ge, J., Giske, J., Groeneveld, J., uuml, rgen, Johnston, A.S.A., Milles, A., Nabe-Nielsen, J., Polhill, J.G., Radchuk, V., Rohw, auml, der, M.-S., Stillman, R.A., Thiele, J.C., Ayll, oacute, n, D., 2020. The ODD Protocol for Describing Agent-Based and Other Simulation Models: A Second Update to Improve Clarity, Replication, and Structural Realism. Journal of Artificial Societies and Social Simulation 23, 7.

Jonson, E., Azar, C., Lindgren, K., Lundberg, L., 2020. Exploring the competition between variable renewable electricity and a carbon-neutral baseload technology. Energy Systems 11, 21-44.

Yang, J., Azar, C., Lindgren, K., 2021. An agent-based model of the transition towards carbon neutrality from the electricity system – investment decisions and uncertainty submitted to Energy Strategy Reviews.

