### Hi there 👋

<!--
**xurenying7465/xurenying7465** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->

data sasuser.transition_cap;
set sasuser.total_2019108;
if age_base=. then delete;
if age_base<18 then delete;
if sex=. then delete;
if bmi_base=. then delete;
if age_base=. then delete;
if sbp_base=. or dbp_base=. then delete;
if fbg_base=. or hba1c_base=. then delete;
if tg_base=. or tc_base=. or hdl_base=. or ldl_base=. then delete;
if cap_base=. then delete;
if uric_acid_base=. then delete;
if hscrp_base=. then delete;
if bmi_base<=14 or bmi_base>=70 then delete;
if sbp_base<60 or sbp_base>=270 then delete;
if dbp_base<30 or dbp_base>=270 then delete;
if ldl_base<0 then delete;
if egfr_base=. then delete;
run;


data a1;
set sasuser.transition_cap;
run;




/*exclusion history of diseases*/
data a2;
set a1;
if hbp_base=1 then delete;
run;

data a3;
set a2;
if dm_base=1 then delete;run;

data a4;
set a3;
if cvd_base=1 then delete;
if stent_base=1 then delete;
if stroke_base=1 then delete;run;

data a5;
set a4;
if dyslipidemia_base=1 then delete;run;


data a7;
set a5;
if cancer_base=1 then delete;
if transplant_base=1 then delete;run;





/*further exclusion baseline metabolic dysfunction*/


data a8;
set a7;

if sbp_base>=130 or dbp_base>=80 then hbp_new=1; else hbp_new=0;

if fbg_base>=5.6 or hba1c_base>=5.7 then dm_new=1; else dm_new=0;

if sex=1 and hdl_base<0.9 then hdl_new=1;
else if sex=1 and hdl_base>=0.9 then hdl_new=0;
else if sex=2 and hdl_base<1 then hdl_new=1;
else if sex=2 and hdl_base>=1 then hdl_new=0;

if tg_base>=1.7 then tg_new=1;
else if tg_base<1.7 then tg_new=0;

if tc_base>=5.72 then tc_new=1;
else if tc_base<5.72 then tc_new=0;

if ldl_base>=3.4 then ldl_new=1;
else if ldl_base<3.4 then ldl_new=0;

run;



data a9;
set a8;
if hbp_new=1 then delete;run;

data a10;
set a9;
if dm_new=1 then delete;run;

data a11;
set a10;
if tc_new=1 then delete;
run;

data a12;
set a11;
if tg_new=1 then delete;
run;

data a13;
set a12;
if ldl_new=1 then delete;
run;

data a14;
set a13;
if hdl_new=1 then delete;
run;


data a17;
set a14;
if bmi_base<=18.4 then delete;
run;


data a19;
set a17;
if bmi_base<24 then bmi_new_g=0;
else if bmi_base>=24  then bmi_new_g=1;

if age_base<65 then age_g=1;
else if age_base>=65 then age_g=2;
run;


data a20;
set a19;
if age_g=2 then delete;
run;

proc freq data=a20;
table cap_base cap_14;
run;

data a21;
set a20;
if cap_base=1 then delete;
run;

data a22;
set a21;
if cap_14=1 then delete;
run;

data a23;
set a22;
if egfr_base<60 then delete;
run;


proc freq data=a23;
table sex;
run;

proc means data=a23;
var age_base;
run;


/*main analysis*/
data a55;
set a23;

if sbp_14>=130 or dbp_14>=80  then hbp_14_high=1; else hbp_14_high=0;
if sbp_15>=130 or dbp_15>=80  then hbp_15_high=1; else hbp_15_high=0;
if sbp_16>=130 or dbp_16>=80  then hbp_16_high=1; else hbp_16_high=0;
if sbp_17>=130 or dbp_17>=80  then hbp_17_high=1; else hbp_17_high=0;
if sbp_18>=130 or dbp_18>=80  then hbp_18_high=1; else hbp_18_high=0;
score_hbp=hbp_14_high+hbp_15_high+hbp_16_high+hbp_17_high+hbp_18_high;
if score_hbp>=1 then hbp_new_g=1;
else if score_hbp<1 then hbp_new_g=0;

if fbg_14>=5.6 or hba1c_14>=5.7  then dm_14_high=1; else dm_14_high=0;
if fbg_15>=5.6 or hba1c_15>=5.7  then dm_15_high=1; else dm_15_high=0;
if fbg_16>=5.6 or hba1c_16>=5.7  then dm_16_high=1; else dm_16_high=0;
if fbg_17>=5.6 or hba1c_17>=5.7  then dm_17_high=1; else dm_17_high=0;
if fbg_18>=5.6 or hba1c_18>=5.7  then dm_18_high=1; else dm_18_high=0;
score_dm=dm_14_high+dm_15_high+dm_16_high+dm_17_high+dm_18_high;
if score_dm>=1 then dm_new_g=1;
else if score_dm<1 then dm_new_g=0;

if  hdl_14=. then hdl_14_low=.;
else if sex=1 and hdl_14<=0.9 then hdl_14_low=1; 
else if sex=2 and hdl_14<=1.0 then hdl_14_low=1;
else hdl_14_low=0;

if  hdl_15=. then hdl_15_low=.;
else if sex=1 and hdl_15<=0.9 then hdl_15_low=1; 
else if sex=2 and hdl_15<=1.0 then hdl_15_low=1;
else hdl_15_low=0;

if  hdl_16=. then hdl_16_low=.;
else if sex=1 and hdl_16<=0.9 then hdl_16_low=1; 
else if sex=2 and hdl_16<=1.0 then hdl_16_low=1;
else hdl_16_low=0;

if  hdl_17=. then hdl_17_low=.;
else if sex=1 and hdl_17<=0.9 then hdl_17_low=1; 
else if sex=2 and hdl_17<=1.0 then hdl_17_low=1;
else hdl_17_low=0;

if  hdl_18=. then hdl_18_low=.;
else if sex=1 and hdl_18<=0.9 then hdl_18_low=1; 
else if sex=2 and hdl_18<=1.0 then hdl_18_low=1;
else hdl_18_low=0;

score_hdl=hdl_14_low+hdl_15_low+hdl_16_low+hdl_17_low+hdl_18_low;
if score_hdl>=1 then hdl_new_g=1;
else if score_hdl<1 then hdl_new_g=0;


if tg_14>=1.7 then tg_14_high=1; else if tg_14<1.7 then tg_14_high=0;
if tg_15>=1.7 then tg_15_high=1; else if tg_15<1.7 then tg_15_high=0;
if tg_16>=1.7 then tg_16_high=1; else if tg_16<1.7 then tg_16_high=0;
if tg_17>=1.7 then tg_17_high=1; else if tg_17<1.7 then tg_17_high=0;
if tg_18>=1.7 then tg_18_high=1; else if tg_18<1.7 then tg_18_high=0;
score_tg=tg_14_high+tg_15_high+tg_16_high+tg_17_high+tg_18_high;
if score_tg>=1 then tg_new_g=1;
else if score_tg<1 then tg_new_g=0;


if tc_14>=5.72 then tc_14_high=1; else if tc_14<5.72 then tc_14_high=0;
if tc_15>=5.72 then tc_15_high=1; else if tc_15<5.72 then tc_15_high=0;
if tc_16>=5.72 then tc_16_high=1; else if tc_16<5.72 then tc_16_high=0;
if tc_17>=5.72 then tc_17_high=1; else if tc_17<5.72 then tc_17_high=0;
if tc_18>=5.72 then tc_18_high=1; else if tc_18<5.72 then tc_18_high=0;
score_tc=tc_14_high+tc_15_high+tc_16_high+tc_17_high+tc_18_high;
if score_tc>=1 then tc_new_g=1;
else if score_tc<1 then tc_new_g=0;

if ldl_14>=3.4 then ldl_14_high=1; else if ldl_14<3.4 then ldl_14_high=0;
if ldl_15>=3.4 then ldl_15_high=1; else if ldl_15<3.4 then ldl_15_high=0;
if ldl_16>=3.4 then ldl_16_high=1; else if ldl_16<3.4 then ldl_16_high=0;
if ldl_17>=3.4 then ldl_17_high=1; else if ldl_17<3.4 then ldl_17_high=0;
if ldl_18>=3.4 then ldl_18_high=1; else if ldl_18<3.4 then ldl_18_high=0;
score_ldl=ldl_14_high+ldl_15_high+ldl_16_high+ldl_17_high+ldl_18_high;
if score_ldl>=1 then ldl_new_g=1;
else if score_ldl<1 then ldl_new_g=0;

score_dyslipidemia_14=hdl_14_low+tc_14_high+tg_14_high+ldl_14_high;
score_dyslipidemia_15=hdl_15_low+tc_15_high+tg_15_high+ldl_15_high;
score_dyslipidemia_16=hdl_16_low+tc_16_high+tg_16_high+ldl_16_high;
score_dyslipidemia_17=hdl_17_low+tc_17_high+tg_17_high+ldl_17_high;
score_dyslipidemia_18=hdl_18_low+tc_18_high+tg_18_high+ldl_18_high;


if score_dyslipidemia_14>=1 then lipid_14=1;
else lipid_14=0;

if score_dyslipidemia_15>=1 then lipid_15=1;
else lipid_15=0;

if score_dyslipidemia_16>=1 then lipid_16=1;
else lipid_16=0;

if score_dyslipidemia_17>=1 then lipid_17=1;
else lipid_17=0;

if score_dyslipidemia_18>=1 then lipid_18=1;
else lipid_18=0;

score_lipid=lipid_14+lipid_15+lipid_16+lipid_17+lipid_18;
if score_lipid>=1 then lipid_new_g=1;
else lipid_new_g=0;
run;


/*figure 1 cumulative incidence of metabolic abnormalities*/
data a100;
set a55;

if hbp_14_high=1 then hbp_14_total=1;
else hbp_14_total=0;

if hbp_14_high=1 then hbp_15_total=1;
else if hbp_14_high=0 and hbp_15_high=1 then hbp_15_total=1;
else if hbp_14_high=0 and hbp_15_high=0 then hbp_15_total=0;

if hbp_14_high=1 or hbp_15_high=1 then hbp_16_total=1;
else if hbp_14_high=0 and hbp_15_high=0 and hbp_16_high=1 then hbp_16_total=1;
else if hbp_14_high=0 and hbp_15_high=0 and hbp_16_high=0 then hbp_16_total=0;

if hbp_14_high=1 or hbp_15_high=1 or hbp_16_high=1 then hbp_17_total=1;
else if hbp_14_high=0 and hbp_15_high=0 and hbp_16_high=0 and hbp_17_high=1 then hbp_17_total=1;
else if hbp_14_high=0 and hbp_15_high=0 and hbp_16_high=0 and hbp_17_high=0 then hbp_17_total=0;

if hbp_14_high=1 or hbp_15_high=1 or hbp_16_high=1 or hbp_17_high=1 then hbp_18_total=1;
else if hbp_14_high=0 and hbp_15_high=0 and hbp_16_high=0 and hbp_17_high=0 and hbp_18_high=1 then hbp_18_total=1;
else if hbp_14_high=0 and hbp_15_high=0 and hbp_16_high=0 and hbp_17_high=0 and hbp_18_high=0 then hbp_18_total=0;


if dm_14_high=1 then dm_14_total=1; else dm_14_total=0;

if dm_14_high=1 then dm_15_total=1;
else if dm_14_high=0 and dm_15_high=1 then dm_15_total=1;
else if dm_14_high=0 and dm_15_high=0 then dm_15_total=0;

if dm_14_high=1 or dm_15_high=1 then dm_16_total=1;
else if dm_14_high=0 and dm_15_high=0 and dm_16_high=1 then dm_16_total=1;
else if dm_14_high=0 and dm_15_high=0 and dm_16_high=0 then dm_16_total=0;

if dm_14_high=1 or dm_15_high=1 or dm_16_high=1 then dm_17_total=1;
else if dm_14_high=0 and dm_15_high=0 and dm_16_high=0 and dm_17_high=1 then dm_17_total=1;
else if dm_14_high=0 and dm_15_high=0 and dm_16_high=0 and dm_17_high=0 then dm_17_total=0;

if dm_14_high=1 or dm_15_high=1 or dm_16_high=1 or dm_17_high=1 then dm_18_total=1;
else if dm_14_high=0 and dm_15_high=0 and dm_16_high=0 and dm_17_high=0 and dm_18_high=1 then dm_18_total=1;
else if dm_14_high=0 and dm_15_high=0 and dm_16_high=0 and dm_17_high=0 and dm_18_high=0 then dm_18_total=0;



if tc_14_high=1 then tc_14_total=1;else tc_14_total=0;

if tc_14_high=1 then tc_15_total=1;
else if tc_14_high=0 and tc_15_high=1 then tc_15_total=1;
else if tc_14_high=0 and tc_15_high=0 then tc_15_total=0;

if tc_14_high=1 or tc_15_high=1 then tc_16_total=1;
else if tc_14_high=0 and tc_15_high=0 and tc_16_high=1 then tc_16_total=1;
else if tc_14_high=0 and tc_15_high=0 and tc_16_high=0 then tc_16_total=0;

if tc_14_high=1 or tc_15_high=1 or tc_16_high=1 then tc_17_total=1;
else if tc_14_high=0 and tc_15_high=0 and tc_16_high=0 and tc_17_high=1 then tc_17_total=1;
else if tc_14_high=0 and tc_15_high=0 and tc_16_high=0 and tc_17_high=0 then tc_17_total=0;

if tc_14_high=1 or tc_15_high=1 or tc_16_high=1 or tc_17_high=1 then tc_18_total=1;
else if tc_14_high=0 and tc_15_high=0 and tc_16_high=0 and tc_17_high=0 and tc_18_high=1 then tc_18_total=1;
else if tc_14_high=0 and tc_15_high=0 and tc_16_high=0 and tc_17_high=0 and tc_18_high=0 then tc_18_total=0;


if tg_14_high=1 then tg_14_total=1;else tg_14_total=0;

if tg_14_high=1 then tg_15_total=1;
else if tg_14_high=0 and tg_15_high=1 then tg_15_total=1;
else if tg_14_high=0 and tg_15_high=0 then tg_15_total=0;

if tg_14_high=1 or tg_15_high=1 then tg_16_total=1;
else if tg_14_high=0 and tg_15_high=0 and tg_16_high=1 then tg_16_total=1;
else if tg_14_high=0 and tg_15_high=0 and tg_16_high=0 then tg_16_total=0;

if tg_14_high=1 or tg_15_high=1 or tg_16_high=1 then tg_17_total=1;
else if tg_14_high=0 and tg_15_high=0 and tg_16_high=0 and tg_17_high=1 then tg_17_total=1;
else if tg_14_high=0 and tg_15_high=0 and tg_16_high=0 and tg_17_high=0 then tg_17_total=0;

if tg_14_high=1 or tg_15_high=1 or tg_16_high=1 or tg_17_high=1 then tg_18_total=1;
else if tg_14_high=0 and tg_15_high=0 and tg_16_high=0 and tg_17_high=0 and tg_18_high=1 then tg_18_total=1;
else if tg_14_high=0 and tg_15_high=0 and tg_16_high=0 and tg_17_high=0 and tg_18_high=0 then tg_18_total=0;



if ldl_14_high=1 then ldl_14_total=1;else ldl_14_total=0;

if ldl_14_high=1 then ldl_15_total=1;
else if ldl_14_high=0 and ldl_15_high=1 then ldl_15_total=1;
else if ldl_14_high=0 and ldl_15_high=0 then ldl_15_total=0;

if ldl_14_high=1 or ldl_15_high=1 then ldl_16_total=1;
else if ldl_14_high=0 and ldl_15_high=0 and ldl_16_high=1 then ldl_16_total=1;
else if ldl_14_high=0 and ldl_15_high=0 and ldl_16_high=0 then ldl_16_total=0;

if ldl_14_high=1 or ldl_15_high=1 or ldl_16_high=1 then ldl_17_total=1;
else if ldl_14_high=0 and ldl_15_high=0 and ldl_16_high=0 and ldl_17_high=1 then ldl_17_total=1;
else if ldl_14_high=0 and ldl_15_high=0 and ldl_16_high=0 and ldl_17_high=0 then ldl_17_total=0;

if ldl_14_high=1 or ldl_15_high=1 or ldl_16_high=1 or ldl_17_high=1 then ldl_18_total=1;
else if ldl_14_high=0 and ldl_15_high=0 and ldl_16_high=0 and ldl_17_high=0 and ldl_18_high=1 then ldl_18_total=1;
else if ldl_14_high=0 and ldl_15_high=0 and ldl_16_high=0 and ldl_17_high=0 and ldl_18_high=0 then ldl_18_total=0;


if hdl_14_low=1 then hdl_14_total=1;else hdl_14_total=0;

if hdl_14_low=1 then hdl_15_total=1;
else if hdl_14_low=0 and hdl_15_low=1 then hdl_15_total=1;
else if hdl_14_low=0 and hdl_15_low=0 then hdl_15_total=0;

if hdl_14_low=1 or hdl_15_low=1 then hdl_16_total=1;
else if hdl_14_low=0 and hdl_15_low=0 and hdl_16_low=1 then hdl_16_total=1;
else if hdl_14_low=0 and hdl_15_low=0 and hdl_16_low=0 then hdl_16_total=0;

if hdl_14_low=1 or hdl_15_low=1 or hdl_16_low=1 then hdl_17_total=1;
else if hdl_14_low=0 and hdl_15_low=0 and hdl_16_low=0 and hdl_17_low=1 then hdl_17_total=1;
else if hdl_14_low=0 and hdl_15_low=0 and hdl_16_low=0 and hdl_17_low=0 then hdl_17_total=0;

if hdl_14_low=1 or hdl_15_low=1 or hdl_16_low=1 or hdl_17_low=1 then hdl_18_total=1;
else if hdl_14_low=0 and hdl_15_low=0 and hdl_16_low=0 and hdl_17_low=0 and hdl_18_low=1 then hdl_18_total=1;
else if hdl_14_low=0 and hdl_15_low=0 and hdl_16_low=0 and hdl_17_low=0 and hdl_18_low=0 then hdl_18_total=0;

run;

proc freq data=a100;
table hbp_14_total hbp_15_total hbp_16_total hbp_17_total hbp_18_total
dm_14_total dm_15_total dm_16_total dm_17_total dm_18_total
tc_14_total tc_15_total tc_16_total tc_17_total tc_18_total
tg_14_total tg_15_total tg_16_total tg_17_total tg_18_total
hdl_14_total hdl_15_total hdl_16_total hdl_17_total hdl_18_total
ldl_14_total ldl_15_total ldl_16_total ldl_17_total ldl_18_total;
run;


/*table 2 baseline body weight status and the risk of metabolic abnormalities*/
data mho_hbp;
set a55;

if hbp_14_high=1  then t_hbp=1;
else if hbp_15_high=1 then t_hbp=2;
else if hbp_16_high=1 then t_hbp=3;
else if hbp_17_high=1 then t_hbp=4;
else if hbp_18_high=1 then t_hbp=5;
else t_hbp=5;

if hbp_14_high=1 then hbp_case=1;
else if hbp_15_high=1 then hbp_case=1;
else if hbp_16_high=1 then hbp_case=1;
else if hbp_17_high=1 then hbp_case=1;
else if hbp_18_high=1 then hbp_case=1;
else hbp_case=0;
run;

proc freq  data=mho_hbp;
table bmi_new_g*hbp_case;
run;

title 'mho and hbp';

proc phreg data=mho_hbp;/*model 1*/
class bmi_new_g(ref="0") sex ;
model t_hbp*hbp_case(0)=bmi_new_g  sex age_base/risklimits;
run;

proc phreg data=mho_hbp;/*model 3*/
class bmi_new_g(ref="0") sex ;
model t_hbp*hbp_case(0)=bmi_new_g  sex age_base sbp_base dbp_base fbg_base hba1c_base  tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;


proc phreg data=mho_hbp;/*model 3*/
class bmi_new_g(ref="0") sex ;
model t_hbp*hbp_case(0)=bmi_new_g  sex age_base sbp_base bmi_new_g*sbp_base dbp_base fbg_base hba1c_base  tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;


data mho_dm;
set a55;

if dm_14_high=1  then t_dm=1;
else if dm_15_high=1 then t_dm=2;
else if dm_16_high=1 then t_dm=3;
else if dm_17_high=1 then t_dm=4;
else if dm_18_high=1 then t_dm=5;
else t_dm=5;

if dm_14_high=1 then dm_case=1;
else if dm_15_high=1 then dm_case=1;
else if dm_16_high=1 then dm_case=1;
else if dm_17_high=1 then dm_case=1;
else if dm_18_high=1 then dm_case=1;
else dm_case=0;
run;

proc freq  data=mho_dm;
table bmi_new_g*dm_case;
run;

title 'mho and dm';
proc phreg data=mho_dm;/*model 3*/
class bmi_new_g(ref="0") sex ;
model t_dm*dm_case(0)=bmi_new_g  sex age_base /risklimits;
run;

proc phreg data=mho_dm;/*model 3*/
class bmi_new_g(ref="0") sex ;
model t_dm*dm_case(0)=bmi_new_g  sex age_base sbp_base dbp_base fbg_base hba1c_base  tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;


proc phreg data=mho_dm;/*model 3*/
class bmi_new_g(ref="0") sex ;
model t_dm*dm_case(0)=bmi_new_g  sex age_base sbp_base dbp_base fbg_base bmi_new_g*fbg_base hba1c_base  tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;


data mho_tc;
set a55;

if tc_14_high=1  then t_tc=1;
else if tc_15_high=1 then t_tc=2;
else if tc_16_high=1  then t_tc=3;
else if tc_17_high=1  then t_tc=4;
else if tc_18_high=1  then t_tc=5;
else t_tc=5;

if tc_14_high=1  then tc_case=1;
else if tc_15_high=1  then tc_case=1;
else if tc_16_high=1  then tc_case=1;
else if tc_17_high=1  then tc_case=1;
else if tc_18_high=1  then tc_case=1;
else tc_case=0;
run;

proc freq  data=mho_tc;
table bmi_new_g*tc_case;
run;

title 'mho and tc';
proc phreg data=mho_tc;/*model 1*/
class bmi_new_g(ref="0") sex ;
model t_tc*tc_case(0)=bmi_new_g  sex age_base/risklimits;
run;

proc phreg data=mho_tc;/*model 3*/
class bmi_new_g(ref="0") sex ;
model t_tc*tc_case(0)=bmi_new_g  sex age_base sbp_base dbp_base fbg_base hba1c_base  tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;

proc phreg data=mho_tc;/*model 3*/
class bmi_new_g(ref="0") sex ;
model t_tc*tc_case(0)=bmi_new_g  sex age_base sbp_base dbp_base fbg_base hba1c_base  bmitc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;

data mho_tg;
set a55;

if tg_14_high=1  then t_tg=1;
else if tg_15_high=1 then t_tg=2;
else if tg_16_high=1  then t_tg=3;
else if tg_17_high=1  then t_tg=4;
else if tg_18_high=1  then t_tg=5;
else t_tg=5;

if tg_14_high=1  then tg_case=1;
else if tg_15_high=1  then tg_case=1;
else if tg_16_high=1  then tg_case=1;
else if tg_17_high=1  then tg_case=1;
else if tg_18_high=1  then tg_case=1;
else tg_case=0;
run;

proc freq  data=mho_tg;
table bmi_new_g*tg_case;
run;

title 'mho and tg';


proc phreg data=mho_tg;/*model 3*/
class bmi_new_g(ref="0") sex ;
model t_tg*tg_case(0)=bmi_new_g  sex age_base /risklimits;
run;

proc phreg data=mho_tg;/*model 3*/
class bmi_new_g(ref="0") sex ;
model t_tg*tg_case(0)=bmi_new_g  sex age_base sbp_base dbp_base fbg_base hba1c_base  tg_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;

data mho_ldl;
set a55;

if ldl_14_high=1  then t_ldl=1;
else if ldl_15_high=1 then t_ldl=2;
else if ldl_16_high=1  then t_ldl=3;
else if ldl_17_high=1  then t_ldl=4;
else if ldl_18_high=1  then t_ldl=5;
else t_ldl=5;

if ldl_14_high=1  then ldl_case=1;
else if ldl_15_high=1  then ldl_case=1;
else if ldl_16_high=1  then ldl_case=1;
else if ldl_17_high=1  then ldl_case=1;
else if ldl_18_high=1  then ldl_case=1;
else ldl_case=0;
run;

proc freq  data=mho_ldl;
table bmi_new_g*ldl_case;
run;

title 'mho and ldl';
proc phreg data=mho_ldl;/*model 1*/
class bmi_new_g(ref="0") sex ;
model t_ldl*ldl_case(0)=bmi_new_g  sex age_base /risklimits;
run;

proc phreg data=mho_ldl;/*model 3*/
class bmi_new_g(ref="0") sex ;
model t_ldl*ldl_case(0)=bmi_new_g  sex age_base sbp_base dbp_base fbg_base hba1c_base  tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;


data mho_hdl;
set a55;

if hdl_14_low=1  then t_hdl=1;
else if hdl_15_low=1 then t_hdl=2;
else if hdl_16_low=1  then t_hdl=3;
else if hdl_17_low=1  then t_hdl=4;
else if hdl_18_low=1  then t_hdl=5;
else t_hdl=5;

if hdl_14_low=1  then hdl_case=1;
else if hdl_15_low=1  then hdl_case=1;
else if hdl_16_low=1  then hdl_case=1;
else if hdl_17_low=1  then hdl_case=1;
else if hdl_18_low=1  then hdl_case=1;
else hdl_case=0;
run;

proc freq  data=mho_hdl;
table bmi_new_g*hdl_case;
run;

title 'mho and hdl';
proc phreg data=mho_hdl;/*model 1*/
class bmi_new_g(ref="0") sex ;
model t_hdl*hdl_case(0)=bmi_new_g  sex age_base /risklimits;
run;

proc phreg data=mho_hdl;/*model 3*/
class bmi_new_g(ref="0") sex ;
model t_hdl*hdl_case(0)=bmi_new_g  sex age_base sbp_base dbp_base fbg_base hba1c_base  tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;



data mho_lipid;
set a55;

if lipid_14=1  then t_lipid=1;
else if lipid_15=1 then t_lipid=2;
else if lipid_16=1  then t_lipid=3;
else if lipid_17=1  then t_lipid=4;
else if lipid_18=1  then t_lipid=5;
else t_lipid=5;

if lipid_14=1  then lipid_case=1;
else if lipid_15=1  then lipid_case=1;
else if lipid_16=1  then lipid_case=1;
else if lipid_17=1  then lipid_case=1;
else if lipid_18=1  then lipid_case=1;
else lipid_case=0;
run;

proc freq  data=mho_lipid;
table bmi_new_g*lipid_case;
run;

title 'mho and lipid';
proc phreg data=mho_lipid;/*model 1*/
class bmi_new_g(ref="0") sex ;
model t_lipid*lipid_case(0)=bmi_new_g  sex age_base /risklimits;
run;


proc phreg data=mho_lipid;/*model 3*/
class bmi_new_g(ref="0") sex ;
model t_lipid*lipid_case(0)=bmi_new_g  sex age_base sbp_base dbp_base fbg_base hba1c_base  tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;




/*transition to abnormal metabolic status and carotid artery plaque*/

data hbp;
set a55;
if hbp_14_high=1 and cap_15=1 then t=1;
else if hbp_14_high=1 and cap_16=1 then t=2;
else if hbp_14_high=1 and cap_17=1 then t=3;
else if hbp_14_high=1 and cap_18=1 then t=4;

else if hbp_14_high=0 and hbp_15_high=1 and cap_16=1 then t=1;
else if hbp_14_high=0 and hbp_15_high=1 and cap_17=1 then t=2;
else if hbp_14_high=0 and hbp_15_high=1 and cap_18=1 then t=3;

else if hbp_14_high=0 and hbp_15_high=0 and hbp_16_high=1 and cap_17=1 then t=1;
else if hbp_14_high=0 and hbp_15_high=0 and hbp_16_high=1 and cap_18=1 then t=2;

else if hbp_14_high=0 and hbp_15_high=0 and hbp_16_high=0 and hbp_17_high=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_hbp=1;
else if cap_16=1 then cap_hbp=1;
else if cap_17=1 then cap_hbp=1;
else if cap_18=1 then cap_hbp=1;
else cap_hbp=0;
run;

proc freq data=hbp;
table bmi_new_g;
run;

data hbp;
set hbp;
if hbp_new_g=0 and bmi_new_g=0 then hbp_new_g1=0;
else if hbp_new_g=1 and bmi_new_g=0 then hbp_new_g1=1;
else if hbp_new_g=0 and bmi_new_g=1 then hbp_new_g1=2;
else if hbp_new_g=1 and bmi_new_g=1 then hbp_new_g1=3;
run;

proc freq data=hbp;
table hbp_new_g1*cap_hbp;
run;

title 'hbp and cap';
proc phreg data=hbp;/*model 3*/
class hbp_new_g1(ref="0") sex;
model t*cap_hbp(0)=hbp_new_g1  sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;


data dm;
set a55;
if dm_14_high=1 and cap_15=1 then t=1;
else if dm_14_high=1 and cap_16=1 then t=2;
else if dm_14_high=1 and cap_17=1 then t=3;
else if dm_14_high=1 and cap_18=1 then t=4;

else if dm_14_high=0 and dm_15_high=1 and cap_16=1 then t=1;
else if dm_14_high=0 and dm_15_high=1 and cap_17=1 then t=2;
else if dm_14_high=0 and dm_15_high=1 and cap_18=1 then t=3;

else if dm_14_high=0 and dm_15_high=0 and dm_16_high=1 and cap_17=1 then t=1;
else if dm_14_high=0 and dm_15_high=0 and dm_16_high=1 and cap_18=1 then t=2;

else if dm_14_high=0 and dm_15_high=0 and dm_16_high=0 and dm_17_high=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_dm=1;
else if cap_16=1 then cap_dm=1;
else if cap_17=1 then cap_dm=1;
else if cap_18=1 then cap_dm=1;
else cap_dm=0;
run;

data dm;
set dm;
if dm_new_g=0  and bmi_new_g=0 then dm_new_g1=0;
else if dm_new_g=1 and bmi_new_g=0 then dm_new_g1=1;
else if dm_new_g=0 and bmi_new_g=1 then dm_new_g1=2;
else if dm_new_g=1 and bmi_new_g=1 then dm_new_g1=3;
run;

proc freq data=dm;
table dm_new_g1*cap_dm;
run;


title 'dm and cap';
proc phreg data=dm;/*model 3*/
class dm_new_g1(ref="0") sex ;
model t*cap_dm(0)=dm_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;



data tc;
set a55;

if tc_14_high=1 and cap_15=1 then t=1;
else if tc_14_high=1 and cap_16=1 then t=2;
else if tc_14_high=1 and cap_17=1 then t=3;
else if tc_14_high=1 and cap_18=1 then t=4;

else if tc_14_high=0 and tc_15_high=1 and cap_16=1 then t=1;
else if tc_14_high=0 and tc_15_high=1 and cap_17=1 then t=2;
else if tc_14_high=0 and tc_15_high=1 and cap_18=1 then t=3;

else if tc_14_high=0 and tc_15_high=0 and tc_16_high=1 and cap_17=1 then t=1;
else if tc_14_high=0 and tc_15_high=0 and tc_16_high=1 and cap_18=1 then t=2;

else if tc_14_high=0 and tc_15_high=0 and tc_16_high=0 and tc_17_high=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_tc=1;
else if cap_16=1 then cap_tc=1;
else if cap_17=1 then cap_tc=1;
else if cap_18=1 then cap_tc=1;
else cap_tc=0;
run;

data tc;
set tc;
if tc_new_g=0  and bmi_new_g=0 then tc_new_g1=0;
else if tc_new_g=1 and bmi_new_g=0 then tc_new_g1=1;
else if tc_new_g=0 and bmi_new_g=1 then tc_new_g1=2;
else if tc_new_g=1 and bmi_new_g=1 then tc_new_g1=3;
run;

proc freq data=tc;
table tc_new_g1*cap_tc;
run;


title 'tc and cap';
proc phreg data=tc;/*model 3*/
class tc_new_g1(ref="0") sex ;
model t*cap_tc(0)=tc_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;



data tg;
set a55;

if tg_14_high=1 and cap_15=1 then t=1;
else if tg_14_high=1 and cap_16=1 then t=2;
else if tg_14_high=1 and cap_17=1 then t=3;
else if tg_14_high=1 and cap_18=1 then t=4;

else if tg_14_high=0 and tg_15_high=1 and cap_16=1 then t=1;
else if tg_14_high=0 and tg_15_high=1 and cap_17=1 then t=2;
else if tg_14_high=0 and tg_15_high=1 and cap_18=1 then t=3;

else if tg_14_high=0 and tg_15_high=0 and tg_16_high=1 and cap_17=1 then t=1;
else if tg_14_high=0 and tg_15_high=0 and tg_16_high=1 and cap_18=1 then t=2;

else if tg_14_high=0 and tg_15_high=0 and tg_16_high=0 and tg_17_high=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_tg=1;
else if cap_16=1 then cap_tg=1;
else if cap_17=1 then cap_tg=1;
else if cap_18=1 then cap_tg=1;
else cap_tg=0;
run;

data tg;
set tg;
if tg_new_g=0  and bmi_new_g=0 then tg_new_g1=0;
else if tg_new_g=1 and bmi_new_g=0 then tg_new_g1=1;
else if tg_new_g=0 and bmi_new_g=1 then tg_new_g1=2;
else if tg_new_g=1 and bmi_new_g=1 then tg_new_g1=3;
run;

proc freq data=tg;
table tg_new_g1*cap_tg;
run;


title 'tg and cap';
proc phreg data=tg;/*model 3*/
class tg_new_g1(ref="0") sex ;
model t*cap_tg(0)=tg_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;



data ldl;
set a55;
if cap_base=1 then delete;
if cap_14=1 then delete;

if ldl_14_high=1 and cap_15=1 then t=1;
else if ldl_14_high=1 and cap_16=1 then t=2;
else if ldl_14_high=1 and cap_17=1 then t=3;
else if ldl_14_high=1 and cap_18=1 then t=4;

else if ldl_14_high=0 and ldl_15_high=1 and cap_16=1 then t=1;
else if ldl_14_high=0 and ldl_15_high=1 and cap_17=1 then t=2;
else if ldl_14_high=0 and ldl_15_high=1 and cap_18=1 then t=3;

else if ldl_14_high=0 and ldl_15_high=0 and ldl_16_high=1 and cap_17=1 then t=1;
else if ldl_14_high=0 and ldl_15_high=0 and ldl_16_high=1 and cap_18=1 then t=2;

else if ldl_14_high=0 and ldl_15_high=0 and ldl_16_high=0 and ldl_17_high=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_ldl=1;
else if cap_16=1 then cap_ldl=1;
else if cap_17=1 then cap_ldl=1;
else if cap_18=1 then cap_ldl=1;
else cap_ldl=0;
run;

data ldl;
set ldl;
if ldl_new_g=0  and bmi_new_g=0 then ldl_new_g1=0;
else if ldl_new_g=1 and bmi_new_g=0 then ldl_new_g1=1;
else if ldl_new_g=0 and bmi_new_g=1 then ldl_new_g1=2;
else if ldl_new_g=1 and bmi_new_g=1 then ldl_new_g1=3;
run;

proc freq data=ldl;
table ldl_new_g1*cap_ldl;
run;


title 'ldl and cap';
proc phreg data=ldl;/*model 3*/
class ldl_new_g1(ref="0") sex ;
model t*cap_ldl(0)=ldl_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base EGFR_base hscrp_base/risklimits;
run;




data hdl;
set a55;
if cap_base=1 then delete;
if cap_14=1 then delete;

if hdl_14_low=1 and cap_15=1 then t=1;
else if hdl_14_low=1 and cap_16=1 then t=2;
else if hdl_14_low=1 and cap_17=1 then t=3;
else if hdl_14_low=1 and cap_18=1 then t=4;

else if hdl_14_low=0 and hdl_15_low=1 and cap_16=1 then t=1;
else if hdl_14_low=0 and hdl_15_low=1 and cap_17=1 then t=2;
else if hdl_14_low=0 and hdl_15_low=1 and cap_18=1 then t=3;

else if hdl_14_low=0 and hdl_15_low=0 and hdl_16_low=1 and cap_17=1 then t=1;
else if hdl_14_low=0 and hdl_15_low=0 and hdl_16_low=1 and cap_18=1 then t=2;

else if hdl_14_low=0 and hdl_15_low=0 and hdl_16_low=0 and hdl_17_low=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_hdl=1;
else if cap_16=1 then cap_hdl=1;
else if cap_17=1 then cap_hdl=1;
else if cap_18=1 then cap_hdl=1;
else cap_hdl=0;
run;

data hdl;
set hdl;
if hdl_new_g=0  and bmi_new_g=0 then hdl_new_g1=0;
else if hdl_new_g=1 and bmi_new_g=0 then hdl_new_g1=1;
else if hdl_new_g=0 and bmi_new_g=1 then hdl_new_g1=2;
else if hdl_new_g=1 and bmi_new_g=1 then hdl_new_g1=3;
run;

proc freq data=hdl;
table hdl_new_g1*cap_hdl;
run;


title 'hdl and cap';
proc phreg data=hdl;/*model 3*/
class hdl_new_g1(ref="0") sex ;
model t*cap_hdl(0)=hdl_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;


data lipid;
set a55;
if lipid_14=1 and cap_15=1 then t=1;
else if lipid_14=1 and cap_16=1 then t=2;
else if lipid_14=1 and cap_17=1 then t=3;
else if lipid_14=1 and cap_18=1 then t=4;

else if lipid_14=0 and lipid_15=1 and cap_16=1 then t=1;
else if lipid_14=0 and lipid_15=1 and cap_17=1 then t=2;
else if lipid_14=0 and lipid_15=1 and cap_18=1 then t=3;

else if lipid_14=0 and lipid_15=0 and lipid_16=1 and cap_17=1 then t=1;
else if lipid_14=0 and lipid_15=0 and lipid_16=1 and cap_18=1 then t=2;

else if lipid_14=0 and lipid_15=0 and lipid_16=0 and lipid_17=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_lipid=1;
else if cap_16=1 then cap_lipid=1;
else if cap_17=1 then cap_lipid=1;
else if cap_18=1 then cap_lipid=1;
else cap_lipid=0;
run;

data lipid;
set lipid;
if lipid_new_g=0  and bmi_new_g=0 then lipid_new_g1=0;
else if lipid_new_g=1 and bmi_new_g=0 then lipid_new_g1=1;
else if lipid_new_g=0 and bmi_new_g=1 then lipid_new_g1=2;
else if lipid_new_g=1 and bmi_new_g=1 then lipid_new_g1=3;
run;

proc freq data=lipid;
table lipid_new_g1*cap_lipid;
run;


title 'lipid and cap';
proc phreg data=lipid;/*model 3*/
class lipid_new_g1(ref="0") sex ;
model t*cap_lipid(0)=lipid_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;


/*sensitivity-1 excluding hscrp>=10*/
data sensitivity_1;
set a55;
if hscrp_base>=10 then delete;
run;

proc freq data=sensitivity_1;
table bmi_new_g;
run;


data hbp_1;
set sensitivity_1;
if hbp_14_high=1 and cap_15=1 then t=1;
else if hbp_14_high=1 and cap_16=1 then t=2;
else if hbp_14_high=1 and cap_17=1 then t=3;
else if hbp_14_high=1 and cap_18=1 then t=4;

else if hbp_14_high=0 and hbp_15_high=1 and cap_16=1 then t=1;
else if hbp_14_high=0 and hbp_15_high=1 and cap_17=1 then t=2;
else if hbp_14_high=0 and hbp_15_high=1 and cap_18=1 then t=3;

else if hbp_14_high=0 and hbp_15_high=0 and hbp_16_high=1 and cap_17=1 then t=1;
else if hbp_14_high=0 and hbp_15_high=0 and hbp_16_high=1 and cap_18=1 then t=2;

else if hbp_14_high=0 and hbp_15_high=0 and hbp_16_high=0 and hbp_17_high=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_hbp=1;
else if cap_16=1 then cap_hbp=1;
else if cap_17=1 then cap_hbp=1;
else if cap_18=1 then cap_hbp=1;
else cap_hbp=0;
run;


data hbp_1;
set hbp_1;
if hbp_new_g=0 and bmi_new_g=0 then hbp_new_g1=0;
else if hbp_new_g=1 and bmi_new_g=0 then hbp_new_g1=1;
else if hbp_new_g=0 and bmi_new_g=1 then hbp_new_g1=2;
else if hbp_new_g=1 and bmi_new_g=1 then hbp_new_g1=3;
run;

proc freq data=hbp_1;
table hbp_new_g1*cap_hbp;
run;


title 'hbp_1 and cap';
proc phreg data=hbp_1;/*model 3*/
class hbp_new_g1(ref="0") sex;
model t*cap_hbp(0)=hbp_new_g1  sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;


data dm_1;
set sensitivity_1;
if dm_14_high=1 and cap_15=1 then t=1;
else if dm_14_high=1 and cap_16=1 then t=2;
else if dm_14_high=1 and cap_17=1 then t=3;
else if dm_14_high=1 and cap_18=1 then t=4;

else if dm_14_high=0 and dm_15_high=1 and cap_16=1 then t=1;
else if dm_14_high=0 and dm_15_high=1 and cap_17=1 then t=2;
else if dm_14_high=0 and dm_15_high=1 and cap_18=1 then t=3;

else if dm_14_high=0 and dm_15_high=0 and dm_16_high=1 and cap_17=1 then t=1;
else if dm_14_high=0 and dm_15_high=0 and dm_16_high=1 and cap_18=1 then t=2;

else if dm_14_high=0 and dm_15_high=0 and dm_16_high=0 and dm_17_high=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_dm=1;
else if cap_16=1 then cap_dm=1;
else if cap_17=1 then cap_dm=1;
else if cap_18=1 then cap_dm=1;
else cap_dm=0;
run;

data dm_1;
set dm_1;
if dm_new_g=0  and bmi_new_g=0 then dm_new_g1=0;
else if dm_new_g=1 and bmi_new_g=0 then dm_new_g1=1;
else if dm_new_g=0 and bmi_new_g=1 then dm_new_g1=2;
else if dm_new_g=1 and bmi_new_g=1 then dm_new_g1=3;
run;

proc freq data=dm_1;
table dm_new_g1*cap_dm;
run;


title 'dm_1 and cap';
proc phreg data=dm_1;/*model 3*/
class dm_new_g1(ref="0") sex ;
model t*cap_dm(0)=dm_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;



data tc_1;
set sensitivity_1;

if tc_14_high=1 and cap_15=1 then t=1;
else if tc_14_high=1 and cap_16=1 then t=2;
else if tc_14_high=1 and cap_17=1 then t=3;
else if tc_14_high=1 and cap_18=1 then t=4;

else if tc_14_high=0 and tc_15_high=1 and cap_16=1 then t=1;
else if tc_14_high=0 and tc_15_high=1 and cap_17=1 then t=2;
else if tc_14_high=0 and tc_15_high=1 and cap_18=1 then t=3;

else if tc_14_high=0 and tc_15_high=0 and tc_16_high=1 and cap_17=1 then t=1;
else if tc_14_high=0 and tc_15_high=0 and tc_16_high=1 and cap_18=1 then t=2;

else if tc_14_high=0 and tc_15_high=0 and tc_16_high=0 and tc_17_high=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_tc=1;
else if cap_16=1 then cap_tc=1;
else if cap_17=1 then cap_tc=1;
else if cap_18=1 then cap_tc=1;
else cap_tc=0;
run;

data tc_1;
set tc_1;
if tc_new_g=0  and bmi_new_g=0 then tc_new_g1=0;
else if tc_new_g=1 and bmi_new_g=0 then tc_new_g1=1;
else if tc_new_g=0 and bmi_new_g=1 then tc_new_g1=2;
else if tc_new_g=1 and bmi_new_g=1 then tc_new_g1=3;
run;

proc freq data=tc_1;
table tc_new_g1*cap_tc;
run;


title 'tc_1 and cap';
proc phreg data=tc_1;/*model 3*/
class tc_new_g1(ref="0") sex ;
model t*cap_tc(0)=tc_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;



data tg_1;
set sensitivity_1;

if tg_14_high=1 and cap_15=1 then t=1;
else if tg_14_high=1 and cap_16=1 then t=2;
else if tg_14_high=1 and cap_17=1 then t=3;
else if tg_14_high=1 and cap_18=1 then t=4;

else if tg_14_high=0 and tg_15_high=1 and cap_16=1 then t=1;
else if tg_14_high=0 and tg_15_high=1 and cap_17=1 then t=2;
else if tg_14_high=0 and tg_15_high=1 and cap_18=1 then t=3;

else if tg_14_high=0 and tg_15_high=0 and tg_16_high=1 and cap_17=1 then t=1;
else if tg_14_high=0 and tg_15_high=0 and tg_16_high=1 and cap_18=1 then t=2;

else if tg_14_high=0 and tg_15_high=0 and tg_16_high=0 and tg_17_high=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_tg=1;
else if cap_16=1 then cap_tg=1;
else if cap_17=1 then cap_tg=1;
else if cap_18=1 then cap_tg=1;
else cap_tg=0;
run;

data tg_1;
set tg_1;
if tg_new_g=0  and bmi_new_g=0 then tg_new_g1=0;
else if tg_new_g=1 and bmi_new_g=0 then tg_new_g1=1;
else if tg_new_g=0 and bmi_new_g=1 then tg_new_g1=2;
else if tg_new_g=1 and bmi_new_g=1 then tg_new_g1=3;
run;

proc freq data=tg_1;
table tg_new_g1*cap_tg;
run;


title 'tg_1 and cap';
proc phreg data=tg_1;/*model 3*/
class tg_new_g1(ref="0") sex ;
model t*cap_tg(0)=tg_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;



data ldl_1;
set sensitivity_1;

if ldl_14_high=1 and cap_15=1 then t=1;
else if ldl_14_high=1 and cap_16=1 then t=2;
else if ldl_14_high=1 and cap_17=1 then t=3;
else if ldl_14_high=1 and cap_18=1 then t=4;

else if ldl_14_high=0 and ldl_15_high=1 and cap_16=1 then t=1;
else if ldl_14_high=0 and ldl_15_high=1 and cap_17=1 then t=2;
else if ldl_14_high=0 and ldl_15_high=1 and cap_18=1 then t=3;

else if ldl_14_high=0 and ldl_15_high=0 and ldl_16_high=1 and cap_17=1 then t=1;
else if ldl_14_high=0 and ldl_15_high=0 and ldl_16_high=1 and cap_18=1 then t=2;

else if ldl_14_high=0 and ldl_15_high=0 and ldl_16_high=0 and ldl_17_high=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_ldl=1;
else if cap_16=1 then cap_ldl=1;
else if cap_17=1 then cap_ldl=1;
else if cap_18=1 then cap_ldl=1;
else cap_ldl=0;
run;

data ldl_1;
set ldl_1;
if ldl_new_g=0  and bmi_new_g=0 then ldl_new_g1=0;
else if ldl_new_g=1 and bmi_new_g=0 then ldl_new_g1=1;
else if ldl_new_g=0 and bmi_new_g=1 then ldl_new_g1=2;
else if ldl_new_g=1 and bmi_new_g=1 then ldl_new_g1=3;
run;

proc freq data=ldl_1;
table ldl_new_g1*cap_ldl;
run;


title 'ldl_1 and cap';
proc phreg data=ldl_1;/*model 3*/
class ldl_new_g1(ref="0") sex ;
model t*cap_ldl(0)=ldl_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base EGFR_base hscrp_base/risklimits;
run;




data hdl_1;
set sensitivity_1;
if cap_base=1 then delete;
if cap_14=1 then delete;

if hdl_14_low=1 and cap_15=1 then t=1;
else if hdl_14_low=1 and cap_16=1 then t=2;
else if hdl_14_low=1 and cap_17=1 then t=3;
else if hdl_14_low=1 and cap_18=1 then t=4;

else if hdl_14_low=0 and hdl_15_low=1 and cap_16=1 then t=1;
else if hdl_14_low=0 and hdl_15_low=1 and cap_17=1 then t=2;
else if hdl_14_low=0 and hdl_15_low=1 and cap_18=1 then t=3;

else if hdl_14_low=0 and hdl_15_low=0 and hdl_16_low=1 and cap_17=1 then t=1;
else if hdl_14_low=0 and hdl_15_low=0 and hdl_16_low=1 and cap_18=1 then t=2;

else if hdl_14_low=0 and hdl_15_low=0 and hdl_16_low=0 and hdl_17_low=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_hdl=1;
else if cap_16=1 then cap_hdl=1;
else if cap_17=1 then cap_hdl=1;
else if cap_18=1 then cap_hdl=1;
else cap_hdl=0;
run;

data hdl_1;
set hdl_1;
if hdl_new_g=0  and bmi_new_g=0 then hdl_new_g1=0;
else if hdl_new_g=1 and bmi_new_g=0 then hdl_new_g1=1;
else if hdl_new_g=0 and bmi_new_g=1 then hdl_new_g1=2;
else if hdl_new_g=1 and bmi_new_g=1 then hdl_new_g1=3;
run;

proc freq data=hdl_1;
table hdl_new_g1*cap_hdl;
run;


title 'hdl_1 and cap';
proc phreg data=hdl_1;/*model 3*/
class hdl_new_g1(ref="0") sex ;
model t*cap_hdl(0)=hdl_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;


data lipid_1;
set sensitivity_1;
if lipid_14=1 and cap_15=1 then t=1;
else if lipid_14=1 and cap_16=1 then t=2;
else if lipid_14=1 and cap_17=1 then t=3;
else if lipid_14=1 and cap_18=1 then t=4;

else if lipid_14=0 and lipid_15=1 and cap_16=1 then t=1;
else if lipid_14=0 and lipid_15=1 and cap_17=1 then t=2;
else if lipid_14=0 and lipid_15=1 and cap_18=1 then t=3;

else if lipid_14=0 and lipid_15=0 and lipid_16=1 and cap_17=1 then t=1;
else if lipid_14=0 and lipid_15=0 and lipid_16=1 and cap_18=1 then t=2;

else if lipid_14=0 and lipid_15=0 and lipid_16=0 and lipid_17=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_lipid=1;
else if cap_16=1 then cap_lipid=1;
else if cap_17=1 then cap_lipid=1;
else if cap_18=1 then cap_lipid=1;
else cap_lipid=0;
run;

data lipid_1;
set lipid_1;
if lipid_new_g=0  and bmi_new_g=0 then lipid_new_g1=0;
else if lipid_new_g=1 and bmi_new_g=0 then lipid_new_g1=1;
else if lipid_new_g=0 and bmi_new_g=1 then lipid_new_g1=2;
else if lipid_new_g=1 and bmi_new_g=1 then lipid_new_g1=3;
run;

proc freq data=lipid_1;
table lipid_new_g1*cap_lipid;
run;


title 'lipid_1 and cap';
proc phreg data=lipid_1;/*model 3*/
class lipid_new_g1(ref="0") sex ;
model t*cap_lipid(0)=lipid_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;

/*sensitivity II: excluding egfr<60*/
data sensitivity_2;
set a55;
run;


data hbp_2;
set sensitivity_2;
if score_hbp=1 then delete;
if score_hbp>1 then hbp_new_g=1;
else if score_hbp<1 then hbp_new_g=0;
run;

data hbp_2;
set hbp_2;
if hbp_14_high=1 and cap_15=1 then t=1;
else if hbp_14_high=1 and cap_16=1 then t=2;
else if hbp_14_high=1 and cap_17=1 then t=3;
else if hbp_14_high=1 and cap_18=1 then t=4;

else if hbp_14_high=0 and hbp_15_high=1 and cap_16=1 then t=1;
else if hbp_14_high=0 and hbp_15_high=1 and cap_17=1 then t=2;
else if hbp_14_high=0 and hbp_15_high=1 and cap_18=1 then t=3;

else if hbp_14_high=0 and hbp_15_high=0 and hbp_16_high=1 and cap_17=1 then t=1;
else if hbp_14_high=0 and hbp_15_high=0 and hbp_16_high=1 and cap_18=1 then t=2;

else if hbp_14_high=0 and hbp_15_high=0 and hbp_16_high=0 and hbp_17_high=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_hbp=1;
else if cap_16=1 then cap_hbp=1;
else if cap_17=1 then cap_hbp=1;
else if cap_18=1 then cap_hbp=1;
else cap_hbp=0;
run;


data hbp_2;
set hbp_2;
if hbp_new_g=0 and bmi_new_g=0 then hbp_new_g1=0;
else if hbp_new_g=1 and bmi_new_g=0 then hbp_new_g1=1;
else if hbp_new_g=0 and bmi_new_g=1 then hbp_new_g1=2;
else if hbp_new_g=1 and bmi_new_g=1 then hbp_new_g1=3;
run;

proc freq data=hbp_2;
table hbp_new_g1*cap_hbp;
run;


title 'hbp_2 and cap';
proc phreg data=hbp_2;/*model 3*/
class hbp_new_g1(ref="0") sex;
model t*cap_hbp(0)=hbp_new_g1  sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;


data dm_2;
set sensitivity_2;
if score_dm=1 then delete;
if score_dm>1 then dm_new_g=1;
else if score_dm<1 then dm_new_g=0;
run;

data dm_2;
set dm_2;
if dm_14_high=1 and cap_15=1 then t=1;
else if dm_14_high=1 and cap_16=1 then t=2;
else if dm_14_high=1 and cap_17=1 then t=3;
else if dm_14_high=1 and cap_18=1 then t=4;

else if dm_14_high=0 and dm_15_high=1 and cap_16=1 then t=1;
else if dm_14_high=0 and dm_15_high=1 and cap_17=1 then t=2;
else if dm_14_high=0 and dm_15_high=1 and cap_18=1 then t=3;

else if dm_14_high=0 and dm_15_high=0 and dm_16_high=1 and cap_17=1 then t=1;
else if dm_14_high=0 and dm_15_high=0 and dm_16_high=1 and cap_18=1 then t=2;

else if dm_14_high=0 and dm_15_high=0 and dm_16_high=0 and dm_17_high=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_dm=1;
else if cap_16=1 then cap_dm=1;
else if cap_17=1 then cap_dm=1;
else if cap_18=1 then cap_dm=1;
else cap_dm=0;
run;

data dm_2;
set dm_2;
if dm_new_g=0  and bmi_new_g=0 then dm_new_g1=0;
else if dm_new_g=1 and bmi_new_g=0 then dm_new_g1=1;
else if dm_new_g=0 and bmi_new_g=1 then dm_new_g1=2;
else if dm_new_g=1 and bmi_new_g=1 then dm_new_g1=3;
run;

proc freq data=dm_2;
table dm_new_g1*cap_dm;
run;


title 'dm_2 and cap';
proc phreg data=dm_2;/*model 3*/
class dm_new_g1(ref="0") sex ;
model t*cap_dm(0)=dm_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;



data tc_2;
set sensitivity_2;
if score_tc=1 then delete;
if score_tc>1 then tc_new_g=1;
else if score_tc<1 then tc_new_g=0;
run;

data tc_2;
set tc_2;
if tc_14_high=1 and cap_15=1 then t=1;
else if tc_14_high=1 and cap_16=1 then t=2;
else if tc_14_high=1 and cap_17=1 then t=3;
else if tc_14_high=1 and cap_18=1 then t=4;

else if tc_14_high=0 and tc_15_high=1 and cap_16=1 then t=1;
else if tc_14_high=0 and tc_15_high=1 and cap_17=1 then t=2;
else if tc_14_high=0 and tc_15_high=1 and cap_18=1 then t=3;

else if tc_14_high=0 and tc_15_high=0 and tc_16_high=1 and cap_17=1 then t=1;
else if tc_14_high=0 and tc_15_high=0 and tc_16_high=1 and cap_18=1 then t=2;

else if tc_14_high=0 and tc_15_high=0 and tc_16_high=0 and tc_17_high=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_tc=1;
else if cap_16=1 then cap_tc=1;
else if cap_17=1 then cap_tc=1;
else if cap_18=1 then cap_tc=1;
else cap_tc=0;
run;

data tc_2;
set tc_2;
if tc_new_g=0  and bmi_new_g=0 then tc_new_g1=0;
else if tc_new_g=1 and bmi_new_g=0 then tc_new_g1=1;
else if tc_new_g=0 and bmi_new_g=1 then tc_new_g1=2;
else if tc_new_g=1 and bmi_new_g=1 then tc_new_g1=3;
run;

proc freq data=tc_2;
table tc_new_g1*cap_tc;
run;


title 'tc_2 and cap';
proc phreg data=tc_2;/*model 3*/
class tc_new_g1(ref="0") sex ;
model t*cap_tc(0)=tc_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;



data tg_2;
set sensitivity_2;
if score_tg=1 then delete;
if score_tg>1 then tg_new_g=1;
else if score_tg<1 then tg_new_g=0;
run;

data tg_2;
set tg_2;
if tg_14_high=1 and cap_15=1 then t=1;
else if tg_14_high=1 and cap_16=1 then t=2;
else if tg_14_high=1 and cap_17=1 then t=3;
else if tg_14_high=1 and cap_18=1 then t=4;

else if tg_14_high=0 and tg_15_high=1 and cap_16=1 then t=1;
else if tg_14_high=0 and tg_15_high=1 and cap_17=1 then t=2;
else if tg_14_high=0 and tg_15_high=1 and cap_18=1 then t=3;

else if tg_14_high=0 and tg_15_high=0 and tg_16_high=1 and cap_17=1 then t=1;
else if tg_14_high=0 and tg_15_high=0 and tg_16_high=1 and cap_18=1 then t=2;

else if tg_14_high=0 and tg_15_high=0 and tg_16_high=0 and tg_17_high=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_tg=1;
else if cap_16=1 then cap_tg=1;
else if cap_17=1 then cap_tg=1;
else if cap_18=1 then cap_tg=1;
else cap_tg=0;
run;

data tg_2;
set tg_2;
if tg_new_g=0  and bmi_new_g=0 then tg_new_g1=0;
else if tg_new_g=1 and bmi_new_g=0 then tg_new_g1=1;
else if tg_new_g=0 and bmi_new_g=1 then tg_new_g1=2;
else if tg_new_g=1 and bmi_new_g=1 then tg_new_g1=3;
run;

proc freq data=tg_2;
table tg_new_g1*cap_tg;
run;


title 'tg_2 and cap';
proc phreg data=tg_2;/*model 3*/
class tg_new_g1(ref="0") sex ;
model t*cap_tg(0)=tg_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;



data ldl_2;
set sensitivity_2;
if score_ldl=1 then delete;
if score_ldl>1 then ldl_new_g=1;
else if score_ldl<1 then ldl_new_g=0;
run;

data ldl_2;
set ldl_2;
if ldl_14_high=1 and cap_15=1 then t=1;
else if ldl_14_high=1 and cap_16=1 then t=2;
else if ldl_14_high=1 and cap_17=1 then t=3;
else if ldl_14_high=1 and cap_18=1 then t=4;

else if ldl_14_high=0 and ldl_15_high=1 and cap_16=1 then t=1;
else if ldl_14_high=0 and ldl_15_high=1 and cap_17=1 then t=2;
else if ldl_14_high=0 and ldl_15_high=1 and cap_18=1 then t=3;

else if ldl_14_high=0 and ldl_15_high=0 and ldl_16_high=1 and cap_17=1 then t=1;
else if ldl_14_high=0 and ldl_15_high=0 and ldl_16_high=1 and cap_18=1 then t=2;

else if ldl_14_high=0 and ldl_15_high=0 and ldl_16_high=0 and ldl_17_high=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_ldl=1;
else if cap_16=1 then cap_ldl=1;
else if cap_17=1 then cap_ldl=1;
else if cap_18=1 then cap_ldl=1;
else cap_ldl=0;
run;

data ldl_2;
set ldl_2;
if ldl_new_g=0  and bmi_new_g=0 then ldl_new_g1=0;
else if ldl_new_g=1 and bmi_new_g=0 then ldl_new_g1=1;
else if ldl_new_g=0 and bmi_new_g=1 then ldl_new_g1=2;
else if ldl_new_g=1 and bmi_new_g=1 then ldl_new_g1=3;
run;

proc freq data=ldl_2;
table ldl_new_g1*cap_ldl;
run;


title 'ldl_2 and cap';
proc phreg data=ldl_2;/*model 3*/
class ldl_new_g1(ref="0") sex ;
model t*cap_ldl(0)=ldl_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base EGFR_base hscrp_base/risklimits;
run;




data hdl_2;
set sensitivity_2;
if score_hdl=1 then delete;
if score_hdl>1 then hdl_new_g=1;
else if score_hdl<1 then hdl_new_g=0;
run;

data hdl_2;
set hdl_2;
if hdl_14_low=1 and cap_15=1 then t=1;
else if hdl_14_low=1 and cap_16=1 then t=2;
else if hdl_14_low=1 and cap_17=1 then t=3;
else if hdl_14_low=1 and cap_18=1 then t=4;

else if hdl_14_low=0 and hdl_15_low=1 and cap_16=1 then t=1;
else if hdl_14_low=0 and hdl_15_low=1 and cap_17=1 then t=2;
else if hdl_14_low=0 and hdl_15_low=1 and cap_18=1 then t=3;

else if hdl_14_low=0 and hdl_15_low=0 and hdl_16_low=1 and cap_17=1 then t=1;
else if hdl_14_low=0 and hdl_15_low=0 and hdl_16_low=1 and cap_18=1 then t=2;

else if hdl_14_low=0 and hdl_15_low=0 and hdl_16_low=0 and hdl_17_low=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_hdl=1;
else if cap_16=1 then cap_hdl=1;
else if cap_17=1 then cap_hdl=1;
else if cap_18=1 then cap_hdl=1;
else cap_hdl=0;
run;

data hdl_2;
set hdl_2;
if hdl_new_g=0  and bmi_new_g=0 then hdl_new_g1=0;
else if hdl_new_g=1 and bmi_new_g=0 then hdl_new_g1=1;
else if hdl_new_g=0 and bmi_new_g=1 then hdl_new_g1=2;
else if hdl_new_g=1 and bmi_new_g=1 then hdl_new_g1=3;
run;

proc freq data=hdl_2;
table hdl_new_g1*cap_hdl;
run;


title 'hdl_2 and cap';
proc phreg data=hdl_2;/*model 3*/
class hdl_new_g1(ref="0") sex ;
model t*cap_hdl(0)=hdl_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;


data lipid_2;
set sensitivity_2;
if score_lipid=1 then delete;
if score_lipid>1 then lipid_new_g=1;
else if score_lipid<1 then lipid_new_g=0;
run;

data lipid_2;
set lipid_2;
if lipid_14=1 and cap_15=1 then t=1;
else if lipid_14=1 and cap_16=1 then t=2;
else if lipid_14=1 and cap_17=1 then t=3;
else if lipid_14=1 and cap_18=1 then t=4;

else if lipid_14=0 and lipid_15=1 and cap_16=1 then t=1;
else if lipid_14=0 and lipid_15=1 and cap_17=1 then t=2;
else if lipid_14=0 and lipid_15=1 and cap_18=1 then t=3;

else if lipid_14=0 and lipid_15=0 and lipid_16=1 and cap_17=1 then t=1;
else if lipid_14=0 and lipid_15=0 and lipid_16=1 and cap_18=1 then t=2;

else if lipid_14=0 and lipid_15=0 and lipid_16=0 and lipid_17=1  and cap_18=1 then t=1;
else t=4;

if cap_15=1 then cap_lipid=1;
else if cap_16=1 then cap_lipid=1;
else if cap_17=1 then cap_lipid=1;
else if cap_18=1 then cap_lipid=1;
else cap_lipid=0;
run;

data lipid_2;
set lipid_2;
if lipid_new_g=0  and bmi_new_g=0 then lipid_new_g1=0;
else if lipid_new_g=1 and bmi_new_g=0 then lipid_new_g1=1;
else if lipid_new_g=0 and bmi_new_g=1 then lipid_new_g1=2;
else if lipid_new_g=1 and bmi_new_g=1 then lipid_new_g1=3;
run;

proc freq data=lipid_2;
table lipid_new_g1*cap_lipid;
run;


title 'lipid_2 and cap';
proc phreg data=lipid_2;/*model 3*/
class lipid_new_g1(ref="0") sex ;
model t*cap_lipid(0)=lipid_new_g1 sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base hscrp_base/risklimits;
run;



/*interaction*/


title 'hbp and cap and sex';
proc phreg data=hbp;/*model 3*/
class hbp_new_g1(ref="0") sex;
model t*cap_hbp(0)=hbp_new_g1  sex hbp_new_g1*sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
run;

proc phreg data=hbp;/*model 3*/
class hbp_new_g1(ref="0");
model t*cap_hbp(0)=hbp_new_g1 age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where sex=1;
run;

proc phreg data=hbp;/*model 3*/
class hbp_new_g1(ref="0");
model t*cap_hbp(0)=hbp_new_g1 age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where sex=2;
run;

proc means data=hbp p50;
var age_base;
run;

data hbp;
set hbp;
if age_base<34 then age_mean=1;
else if age_base>=34 then age_mean=2;
run;


title 'hbp and cap and age';
proc phreg data=hbp;/*model 3*/
class hbp_new_g1(ref="0") sex age_mean;
model t*cap_hbp(0)=hbp_new_g1  sex hbp_new_g1*age_mean  sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
run;

proc phreg data=hbp;/*model 3*/
class hbp_new_g1(ref="0") sex ;
model t*cap_hbp(0)=hbp_new_g1  sex   sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where age_mean=1;
run;

proc phreg data=hbp;/*model 3*/
class hbp_new_g1(ref="0") sex ;
model t*cap_hbp(0)=hbp_new_g1  sex   sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where age_mean=2;
run;





title 'dm and cap and sex';
proc phreg data=dm;/*model 3*/
class dm_new_g1(ref="0") sex;
model t*cap_dm(0)=dm_new_g1  sex dm_new_g1*sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
run;

proc phreg data=dm;/*model 3*/
class dm_new_g1(ref="0");
model t*cap_dm(0)=dm_new_g1  age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where sex=1;
run;

proc phreg data=dm;/*model 3*/
class dm_new_g1(ref="0");
model t*cap_dm(0)=dm_new_g1  age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where sex=2;
run;

data dm;
set dm;
if age_base<34 then age_mean=1;
else if age_base>=34 then age_mean=2;
run;


title 'dm and cap and age';
proc phreg data=dm;/*model 3*/
class dm_new_g1(ref="0") sex age_mean;
model t*cap_dm(0)=dm_new_g1  sex dm_new_g1*age_mean  sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
run;

proc phreg data=dm;/*model 3*/
class dm_new_g1(ref="0") sex;
model t*cap_dm(0)=dm_new_g1  sex   sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where age_mean=1;
run;

proc phreg data=dm;/*model 3*/
class dm_new_g1(ref="0") sex;
model t*cap_dm(0)=dm_new_g1  sex   sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where age_mean=2;
run;


title 'lipid and cap and sex';
proc phreg data=lipid;/*model 3*/
class lipid_new_g1(ref="0") sex;
model t*cap_lipid(0)=lipid_new_g1  sex lipid_new_g1*sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
run;

proc phreg data=lipid;/*model 3*/
class lipid_new_g1(ref="0");
model t*cap_lipid(0)=lipid_new_g1 age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where sex=1;
run;

proc phreg data=lipid;/*model 3*/
class lipid_new_g1(ref="0");
model t*cap_lipid(0)=lipid_new_g1 age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where sex=2;
run;

data lipid;
set lipid;
if age_base<34 then age_mean=1;
else if age_base>=34 then age_mean=2;
run;


title 'lipid and cap and age';
proc phreg data=lipid;/*model 3*/
class lipid_new_g1(ref="0") sex age_mean;
model t*cap_lipid(0)=lipid_new_g1  sex lipid_new_g1*age_mean  sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
run;

proc phreg data=lipid;/*model 3*/
class lipid_new_g1(ref="0") sex ;
model t*cap_lipid(0)=lipid_new_g1  sex  sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where age_mean=1;
run;

proc phreg data=lipid;/*model 3*/
class lipid_new_g1(ref="0") sex ;
model t*cap_lipid(0)=lipid_new_g1  sex  sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where age_mean=2;
run;



title 'tc and cap and sex';
proc phreg data=tc;/*model 3*/
class tc_new_g1(ref="0") sex;
model t*cap_tc(0)=tc_new_g1  sex tc_new_g1*sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
run;

proc phreg data=tc;/*model 3*/
class tc_new_g1(ref="0");
model t*cap_tc(0)=tc_new_g1  age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where sex=1;
run;

proc phreg data=tc;/*model 3*/
class tc_new_g1(ref="0");
model t*cap_tc(0)=tc_new_g1  age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where sex=2;
run;


data tc;
set tc;
if age_base<34 then age_mean=1;
else if age_base>=34 then age_mean=2;
run;


title 'tc and cap and age';
proc phreg data=tc;/*model 3*/
class tc_new_g1(ref="0") sex age_mean;
model t*cap_tc(0)=tc_new_g1  sex tc_new_g1*age_mean  sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
run;

proc phreg data=tc;/*model 3*/
class tc_new_g1(ref="0") sex;
model t*cap_tc(0)=tc_new_g1  sex   sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where age_mean=1;
run;

proc phreg data=tc;/*model 3*/
class tc_new_g1(ref="0") sex;
model t*cap_tc(0)=tc_new_g1  sex   sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where age_mean=2;
run;


title 'tg and cap and sex';
proc phreg data=tg;/*model 3*/
class tg_new_g1(ref="0") sex;
model t*cap_tg(0)=tg_new_g1  sex tg_new_g1*sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
run;

proc phreg data=tg;/*model 3*/
class tg_new_g1(ref="0");
model t*cap_tg(0)=tg_new_g1  age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where sex=1;
run;

proc phreg data=tg;/*model 3*/
class tg_new_g1(ref="0");
model t*cap_tg(0)=tg_new_g1  age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where sex=2;
run;


data tg;
set tg;
if age_base<34 then age_mean=1;
else if age_base>=34 then age_mean=2;
run;


title 'tg and cap and age';
proc phreg data=tg;/*model 3*/
class tg_new_g1(ref="0") sex age_mean;
model t*cap_tg(0)=tg_new_g1  sex tg_new_g1*age_mean  sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
run;

proc phreg data=tg;/*model 3*/
class tg_new_g1(ref="0") sex;
model t*cap_tg(0)=tg_new_g1  sex   sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where age_mean=1;
run;


proc phreg data=tg;/*model 3*/
class tg_new_g1(ref="0") sex;
model t*cap_tg(0)=tg_new_g1  sex   sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where age_mean=2;
run;


title 'ldl and cap and sex';
proc phreg data=ldl;/*model 3*/
class ldl_new_g1(ref="0") sex;
model t*cap_ldl(0)=ldl_new_g1  sex ldl_new_g1*sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
run;

proc phreg data=ldl;/*model 3*/
class ldl_new_g1(ref="0");
model t*cap_ldl(0)=ldl_new_g1  age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where sex=1;
run;

proc phreg data=ldl;/*model 3*/
class ldl_new_g1(ref="0");
model t*cap_ldl(0)=ldl_new_g1  age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where sex=2;
run;


data ldl;
set ldl;
if age_base<34 then age_mean=1;
else if age_base>=34 then age_mean=2;
run;


title 'ldl and cap and age';
proc phreg data=ldl;/*model 3*/
class ldl_new_g1(ref="0") sex age_mean;
model t*cap_ldl(0)=ldl_new_g1  sex ldl_new_g1*age_mean  sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
run;

proc phreg data=ldl;/*model 3*/
class ldl_new_g1(ref="0") sex;
model t*cap_ldl(0)=ldl_new_g1  sex   sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where age_mean=1;
run;

proc phreg data=ldl;/*model 3*/
class ldl_new_g1(ref="0") sex;
model t*cap_ldl(0)=ldl_new_g1  sex   sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where age_mean=2;
run;



title 'hdl and cap and sex';
proc phreg data=hdl;/*model 3*/
class hdl_new_g1(ref="0") sex;
model t*cap_hdl(0)=hdl_new_g1  sex hdl_new_g1*sex age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
run;

proc phreg data=hdl;/*model 3*/
class hdl_new_g1(ref="0");
model t*cap_hdl(0)=hdl_new_g1  age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where sex=1;
run;


proc phreg data=hdl;/*model 3*/
class hdl_new_g1(ref="0");
model t*cap_hdl(0)=hdl_new_g1  age_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where sex=2;
run;



data hdl;
set hdl;
if age_base<34 then age_mean=1;
else if age_base>=34 then age_mean=2;
run;


title 'hdl and cap and age';
proc phreg data=hdl;/*model 3*/
class hdl_new_g1(ref="0") sex age_mean;
model t*cap_hdl(0)=hdl_new_g1  sex hdl_new_g1*age_mean  sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
run;

proc phreg data=hdl;/*model 3*/
class hdl_new_g1(ref="0") sex;
model t*cap_hdl(0)=hdl_new_g1  sex   sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where age_mean=1;
run;

proc phreg data=hdl;/*model 3*/
class hdl_new_g1(ref="0") sex;
model t*cap_hdl(0)=hdl_new_g1  sex   sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base/risklimits;
where age_mean=2;
run;


/*table 1*/
proc freq data=a55;
table sex bmi_new_g;
run;


proc freq data=a55;
table bmi_new_g*sex ;
run;


proc npar1way data=a55 wilcoxon;
      class bmi_new_g;
      var sex hscrp_base;
   run;

proc means data=a55;
var age_base;
run;


  proc means data=a55;
   var age_base bmi_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base hscrp_base egfr_base;
   class bmi_new_g;
   run;

proc means data=a55 p25 p50 p75;
var hscrp_base;
class bmi_new_g;
run;

proc anova data=a55;
class bmi_new_g;
model bmi_base age_base  sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base=bmi_new_g;
run; 

/*comparison between participants in and out of the study*/
proc freq data=a23;
table sex;
run;

  proc means data=a23;
   var age_base bmi_base sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base hscrp_base egfr_base;
   run;

proc means data=a23 p25 p50 p75;
var hscrp_base;
run;


data a300; set a23; if age_base ne . then group_new=1;run;

proc sort data=a300;
by id;
run;

proc sort data=a1;
by id;
run;


data compare;
     merge a300 a1;
     by id;
run;

data compare;
set compare;
if group_new=1 then group_compare=1;
else if group_new=. then group_compare=2;
run;


proc freq data=compare;
table group_compare;
run;



proc freq data=compare;
table group_compare*sex;
run;

proc npar1way data=compare wilcoxon;
      class group_compare;
      var sex;
   run;

    proc means data=compare;
   var bmi_base age_base  sbp_base dbp_base fbg_base hba1c_base  tc_base tg_base hdl_base ldl_base egfr_base;
class group_compare;
run;

 
proc anova data=compare;
class group_compare;
model bmi_base age_base  sbp_base dbp_base fbg_base hba1c_base tc_base tg_base hdl_base ldl_base egfr_base=group_compare;
run; 

 proc means data=compare p50 p25 p75;
   var hscrp_base;
class group_compare;
run;

