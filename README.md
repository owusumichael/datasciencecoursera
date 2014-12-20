# Project Assignment Report for Coursera Practical Machine Learning
# R version used
#R version 3.0.2 (2013-09-25) -- "Frisbee Sailing"
#Copyright (C) 2013 The R Foundation for Statistical Computing
#Platform: x86_64-w64-mingw32/x64 (64-bit)

# Main Assignment
#The goal of this project is to predict "how well" six young men performed barbell lifts  
# The variable "classe" shows classifications of how these exercises were done to the specifications of classe A:
#Class B : throwing the elbows to the front 
#Class C: lifting the dumbbell only halfway 
#Class D lowering the dumbbell only halfway 
#Class E throwing the hips to the front 
#==================================================================#
#Class A corresponds to the specified execution of the exercise, 
#while the other 4 classes correspond to common mistakes.

#Question 1: You should create a report describing how you built your model


#============ Upload libraries needed to build models======================#

> library(AppliedPredictiveModeling)
Warning message:
package ‘AppliedPredictiveModeling’ was built under R version 3.0.3 

> library(caret)
Loading required package: ggplot2

Attaching package: ‘caret’

The following object is masked from ‘package:survival’:

    cluster

> library(rattle)
Rattle: A free graphical interface for data mining with R.
Version 3.3.0 Copyright (c) 2006-2014 Togaware Pty Ltd.
Type 'rattle()' to shake, rattle, and roll your data.
Warning message:
package ‘rattle’ was built under R version 3.0.3 

> library(rpart.plot)
Loading required package: rpart
Warning message:
package ‘rpart.plot’ was built under R version 3.0.3 

> library(randomForest)
randomForest 4.6-10
Type rfNews() to see new features/changes/bug fixes.

Attaching package: ‘randomForest’

The following object is masked from ‘package:Hmisc’:

    combine

Warning message:
package ‘randomForest’ was built under R version 3.0.3 

#============ Download datasets into my local drive and upload datasets unto R==========#
> training.as<-read.csv("pml-training.csv",as.is=TRUE)
> testing.as<- read.csv("pml-testing.csv",as.is=TRUE)
> training.as$classe<-factor(training.as$classe)
> training.as$classe<-factor(training.as$classe)
> des(training.as)
 
 No. of observations =  19622 
    Variable                 Class           Description
1   X                        integer                    
2   user_name                character                  
3   raw_timestamp_part_1     integer                    
4   raw_timestamp_part_2     integer                    
5   cvtd_timestamp           character                  
6   new_window               character                  
7   num_window               integer                    
8   roll_belt                numeric                    
9   pitch_belt               numeric                    
10  yaw_belt                 numeric                    
11  total_accel_belt         integer                    
12  kurtosis_roll_belt       character                  
13  kurtosis_picth_belt      character                  
14  kurtosis_yaw_belt        character                  
15  skewness_roll_belt       character                  
16  skewness_roll_belt.1     character                  
17  skewness_yaw_belt        character                  
18  max_roll_belt            numeric                    
19  max_picth_belt           integer                    
20  max_yaw_belt             character                  
21  min_roll_belt            numeric                    
22  min_pitch_belt           integer                    
23  min_yaw_belt             character                  
24  amplitude_roll_belt      numeric                    
25  amplitude_pitch_belt     integer                    
26  amplitude_yaw_belt       character                  
27  var_total_accel_belt     numeric                    
28  avg_roll_belt            numeric                    
29  stddev_roll_belt         numeric                    
30  var_roll_belt            numeric                    
31  avg_pitch_belt           numeric                    
32  stddev_pitch_belt        numeric                    
33  var_pitch_belt           numeric                    
34  avg_yaw_belt             numeric                    
35  stddev_yaw_belt          numeric                    
36  var_yaw_belt             numeric                    
37  gyros_belt_x             numeric                    
38  gyros_belt_y             numeric                    
39  gyros_belt_z             numeric                    
40  accel_belt_x             integer                    
41  accel_belt_y             integer                    
42  accel_belt_z             integer                    
43  magnet_belt_x            integer                    
44  magnet_belt_y            integer                    
45  magnet_belt_z            integer                    
46  roll_arm                 numeric                    
47  pitch_arm                numeric                    
48  yaw_arm                  numeric                    
49  total_accel_arm          integer                    
50  var_accel_arm            numeric                    
51  avg_roll_arm             numeric                    
52  stddev_roll_arm          numeric                    
53  var_roll_arm             numeric                    
54  avg_pitch_arm            numeric                    
55  stddev_pitch_arm         numeric                    
56  var_pitch_arm            numeric                    
57  avg_yaw_arm              numeric                    
58  stddev_yaw_arm           numeric                    
59  var_yaw_arm              numeric                    
60  gyros_arm_x              numeric                    
61  gyros_arm_y              numeric                    
62  gyros_arm_z              numeric                    
63  accel_arm_x              integer                    
64  accel_arm_y              integer                    
65  accel_arm_z              integer                    
66  magnet_arm_x             integer                    
67  magnet_arm_y             integer                    
68  magnet_arm_z             integer                    
69  kurtosis_roll_arm        character                  
70  kurtosis_picth_arm       character                  
71  kurtosis_yaw_arm         character                  
72  skewness_roll_arm        character                  
73  skewness_pitch_arm       character                  
74  skewness_yaw_arm         character                  
75  max_roll_arm             numeric                    
76  max_picth_arm            numeric                    
77  max_yaw_arm              integer                    
78  min_roll_arm             numeric                    
79  min_pitch_arm            numeric                    
80  min_yaw_arm              integer                    
81  amplitude_roll_arm       numeric                    
82  amplitude_pitch_arm      numeric                    
83  amplitude_yaw_arm        integer                    
84  roll_dumbbell            numeric                    
85  pitch_dumbbell           numeric                    
86  yaw_dumbbell             numeric                    
87  kurtosis_roll_dumbbell   character                  
88  kurtosis_picth_dumbbell  character                  
89  kurtosis_yaw_dumbbell    character                  
90  skewness_roll_dumbbell   character                  
91  skewness_pitch_dumbbell  character                  
92  skewness_yaw_dumbbell    character                  
93  max_roll_dumbbell        numeric                    
94  max_picth_dumbbell       numeric                    
95  max_yaw_dumbbell         character                  
96  min_roll_dumbbell        numeric                    
97  min_pitch_dumbbell       numeric                    
98  min_yaw_dumbbell         character                  
99  amplitude_roll_dumbbell  numeric                    
100 amplitude_pitch_dumbbell numeric                    
101 amplitude_yaw_dumbbell   character                  
102 total_accel_dumbbell     integer                    
103 var_accel_dumbbell       numeric                    
104 avg_roll_dumbbell        numeric                    
105 stddev_roll_dumbbell     numeric                    
106 var_roll_dumbbell        numeric                    
107 avg_pitch_dumbbell       numeric                    
108 stddev_pitch_dumbbell    numeric                    
109 var_pitch_dumbbell       numeric                    
110 avg_yaw_dumbbell         numeric                    
111 stddev_yaw_dumbbell      numeric                    
112 var_yaw_dumbbell         numeric                    
113 gyros_dumbbell_x         numeric                    
114 gyros_dumbbell_y         numeric                    
115 gyros_dumbbell_z         numeric                    
116 accel_dumbbell_x         integer                    
117 accel_dumbbell_y         integer                    
118 accel_dumbbell_z         integer                    
119 magnet_dumbbell_x        integer                    
120 magnet_dumbbell_y        integer                    
121 magnet_dumbbell_z        numeric                    
122 roll_forearm             numeric                    
123 pitch_forearm            numeric                    
124 yaw_forearm              numeric                    
125 kurtosis_roll_forearm    character                  
126 kurtosis_picth_forearm   character                  
127 kurtosis_yaw_forearm     character                  
128 skewness_roll_forearm    character                  
129 skewness_pitch_forearm   character                  
130 skewness_yaw_forearm     character                  
131 max_roll_forearm         numeric                    
132 max_picth_forearm        numeric                    
133 max_yaw_forearm          character                  
134 min_roll_forearm         numeric                    
135 min_pitch_forearm        numeric                    
136 min_yaw_forearm          character                  
137 amplitude_roll_forearm   numeric                    
138 amplitude_pitch_forearm  numeric                    
139 amplitude_yaw_forearm    character                  
140 total_accel_forearm      integer                    
141 var_accel_forearm        numeric                    
142 avg_roll_forearm         numeric                    
143 stddev_roll_forearm      numeric                    
144 var_roll_forearm         numeric                    
145 avg_pitch_forearm        numeric                    
146 stddev_pitch_forearm     numeric                    
147 var_pitch_forearm        numeric                    
148 avg_yaw_forearm          numeric                    
149 stddev_yaw_forearm       numeric                    
150 var_yaw_forearm          numeric                    
151 gyros_forearm_x          numeric                    
152 gyros_forearm_y          numeric                    
153 gyros_forearm_z          numeric                    
154 accel_forearm_x          integer                    
155 accel_forearm_y          integer                    
156 accel_forearm_z          integer                    
157 magnet_forearm_x         integer                    
158 magnet_forearm_y         numeric                    
159 magnet_forearm_z         numeric                    
160 classe                   factor                     
> des(testing.as)
 
 No. of observations =  20 
    Variable                 Class           Description
1   X                        integer                    
2   user_name                character                  
3   raw_timestamp_part_1     integer                    
4   raw_timestamp_part_2     integer                    
5   cvtd_timestamp           character                  
6   new_window               character                  
7   num_window               integer                    
8   roll_belt                numeric                    
9   pitch_belt               numeric                    
10  yaw_belt                 numeric                    
11  total_accel_belt         integer                    
12  kurtosis_roll_belt       logical                    
13  kurtosis_picth_belt      logical                    
14  kurtosis_yaw_belt        logical                    
15  skewness_roll_belt       logical                    
16  skewness_roll_belt.1     logical                    
17  skewness_yaw_belt        logical                    
18  max_roll_belt            logical                    
19  max_picth_belt           logical                    
20  max_yaw_belt             logical                    
21  min_roll_belt            logical                    
22  min_pitch_belt           logical                    
23  min_yaw_belt             logical                    
24  amplitude_roll_belt      logical                    
25  amplitude_pitch_belt     logical                    
26  amplitude_yaw_belt       logical                    
27  var_total_accel_belt     logical                    
28  avg_roll_belt            logical                    
29  stddev_roll_belt         logical                    
30  var_roll_belt            logical                    
31  avg_pitch_belt           logical                    
32  stddev_pitch_belt        logical                    
33  var_pitch_belt           logical                    
34  avg_yaw_belt             logical                    
35  stddev_yaw_belt          logical                    
36  var_yaw_belt             logical                    
37  gyros_belt_x             numeric                    
38  gyros_belt_y             numeric                    
39  gyros_belt_z             numeric                    
40  accel_belt_x             integer                    
41  accel_belt_y             integer                    
42  accel_belt_z             integer                    
43  magnet_belt_x            integer                    
44  magnet_belt_y            integer                    
45  magnet_belt_z            integer                    
46  roll_arm                 numeric                    
47  pitch_arm                numeric                    
48  yaw_arm                  numeric                    
49  total_accel_arm          integer                    
50  var_accel_arm            logical                    
51  avg_roll_arm             logical                    
52  stddev_roll_arm          logical                    
53  var_roll_arm             logical                    
54  avg_pitch_arm            logical                    
55  stddev_pitch_arm         logical                    
56  var_pitch_arm            logical                    
57  avg_yaw_arm              logical                    
58  stddev_yaw_arm           logical                    
59  var_yaw_arm              logical                    
60  gyros_arm_x              numeric                    
61  gyros_arm_y              numeric                    
62  gyros_arm_z              numeric                    
63  accel_arm_x              integer                    
64  accel_arm_y              integer                    
65  accel_arm_z              integer                    
66  magnet_arm_x             integer                    
67  magnet_arm_y             integer                    
68  magnet_arm_z             integer                    
69  kurtosis_roll_arm        logical                    
70  kurtosis_picth_arm       logical                    
71  kurtosis_yaw_arm         logical                    
72  skewness_roll_arm        logical                    
73  skewness_pitch_arm       logical                    
74  skewness_yaw_arm         logical                    
75  max_roll_arm             logical                    
76  max_picth_arm            logical                    
77  max_yaw_arm              logical                    
78  min_roll_arm             logical                    
79  min_pitch_arm            logical                    
80  min_yaw_arm              logical                    
81  amplitude_roll_arm       logical                    
82  amplitude_pitch_arm      logical                    
83  amplitude_yaw_arm        logical                    
84  roll_dumbbell            numeric                    
85  pitch_dumbbell           numeric                    
86  yaw_dumbbell             numeric                    
87  kurtosis_roll_dumbbell   logical                    
88  kurtosis_picth_dumbbell  logical                    
89  kurtosis_yaw_dumbbell    logical                    
90  skewness_roll_dumbbell   logical                    
91  skewness_pitch_dumbbell  logical                    
92  skewness_yaw_dumbbell    logical                    
93  max_roll_dumbbell        logical                    
94  max_picth_dumbbell       logical                    
95  max_yaw_dumbbell         logical                    
96  min_roll_dumbbell        logical                    
97  min_pitch_dumbbell       logical                    
98  min_yaw_dumbbell         logical                    
99  amplitude_roll_dumbbell  logical                    
100 amplitude_pitch_dumbbell logical                    
101 amplitude_yaw_dumbbell   logical                    
102 total_accel_dumbbell     integer                    
103 var_accel_dumbbell       logical                    
104 avg_roll_dumbbell        logical                    
105 stddev_roll_dumbbell     logical                    
106 var_roll_dumbbell        logical                    
107 avg_pitch_dumbbell       logical                    
108 stddev_pitch_dumbbell    logical                    
109 var_pitch_dumbbell       logical                    
110 avg_yaw_dumbbell         logical                    
111 stddev_yaw_dumbbell      logical                    
112 var_yaw_dumbbell         logical                    
113 gyros_dumbbell_x         numeric                    
114 gyros_dumbbell_y         numeric                    
115 gyros_dumbbell_z         numeric                    
116 accel_dumbbell_x         integer                    
117 accel_dumbbell_y         integer                    
118 accel_dumbbell_z         integer                    
119 magnet_dumbbell_x        integer                    
120 magnet_dumbbell_y        integer                    
121 magnet_dumbbell_z        integer                    
122 roll_forearm             numeric                    
123 pitch_forearm            numeric                    
124 yaw_forearm              numeric                    
125 kurtosis_roll_forearm    logical                    
126 kurtosis_picth_forearm   logical                    
127 kurtosis_yaw_forearm     logical                    
128 skewness_roll_forearm    logical                    
129 skewness_pitch_forearm   logical                    
130 skewness_yaw_forearm     logical                    
131 max_roll_forearm         logical                    
132 max_picth_forearm        logical                    
133 max_yaw_forearm          logical                    
134 min_roll_forearm         logical                    
135 min_pitch_forearm        logical                    
136 min_yaw_forearm          logical                    
137 amplitude_roll_forearm   logical                    
138 amplitude_pitch_forearm  logical                    
139 amplitude_yaw_forearm    logical                    
140 total_accel_forearm      integer                    
141 var_accel_forearm        logical                    
142 avg_roll_forearm         logical                    
143 stddev_roll_forearm      logical                    
144 var_roll_forearm         logical                    
145 avg_pitch_forearm        logical                    
146 stddev_pitch_forearm     logical                    
147 var_pitch_forearm        logical                    
148 avg_yaw_forearm          logical                    
149 stddev_yaw_forearm       logical                    
150 var_yaw_forearm          logical                    
151 gyros_forearm_x          numeric                    
152 gyros_forearm_y          numeric                    
153 gyros_forearm_z          numeric                    
154 accel_forearm_x          integer                    
155 accel_forearm_y          integer                    
156 accel_forearm_z          integer                    
157 magnet_forearm_x         integer                    
158 magnet_forearm_y         integer                    
159 magnet_forearm_z         integer                    
160 problem_id               integer 

#===================== Clean datasets for unwanted variables=========================#

#============Eliminate NA colums  in training and test datasets======================#
                   
> colnames_train <- colnames(training.as)
> without.NAs <- function(x) {
+ as.vector(apply(x, 2, function(x) length(which(!is.na(x)))))
+ }


# Build vector of missing data or NA columns to drop.
> col.to.drop <- without.NAs(training.as)
> drops <- c()
> for (i in 1:length(col.to.drop)) {
+ if (col.to.drop[i] < nrow(training.as)) {
+ drops <- c(drops, colnames_train[i])
+ }
+ }
> training.as <- training.as[,!(names(training.as) %in% drops)]
> testing.as <- testing.as[,!(names(testing.as) %in% drops)]
> des(training.as)
 
 No. of observations =  19622 
   Variable                Class           Description
1  X                       integer                    
2  user_name               character                  
3  raw_timestamp_part_1    integer                    
4  raw_timestamp_part_2    integer                    
5  cvtd_timestamp          character                  
6  new_window              character                  
7  num_window              integer                    
8  roll_belt               numeric                    
9  pitch_belt              numeric                    
10 yaw_belt                numeric                    
11 total_accel_belt        integer                    
12 kurtosis_roll_belt      character                  
13 kurtosis_picth_belt     character                  
14 kurtosis_yaw_belt       character                  
15 skewness_roll_belt      character                  
16 skewness_roll_belt.1    character                  
17 skewness_yaw_belt       character                  
18 max_yaw_belt            character                  
19 min_yaw_belt            character                  
20 amplitude_yaw_belt      character                  
21 gyros_belt_x            numeric                    
22 gyros_belt_y            numeric                    
23 gyros_belt_z            numeric                    
24 accel_belt_x            integer                    
25 accel_belt_y            integer                    
26 accel_belt_z            integer                    
27 magnet_belt_x           integer                    
28 magnet_belt_y           integer                    
29 magnet_belt_z           integer                    
30 roll_arm                numeric                    
31 pitch_arm               numeric                    
32 yaw_arm                 numeric                    
33 total_accel_arm         integer                    
34 gyros_arm_x             numeric                    
35 gyros_arm_y             numeric                    
36 gyros_arm_z             numeric                    
37 accel_arm_x             integer                    
38 accel_arm_y             integer                    
39 accel_arm_z             integer                    
40 magnet_arm_x            integer                    
41 magnet_arm_y            integer                    
42 magnet_arm_z            integer                    
43 kurtosis_roll_arm       character                  
44 kurtosis_picth_arm      character                  
45 kurtosis_yaw_arm        character                  
46 skewness_roll_arm       character                  
47 skewness_pitch_arm      character                  
48 skewness_yaw_arm        character                  
49 roll_dumbbell           numeric                    
50 pitch_dumbbell          numeric                    
51 yaw_dumbbell            numeric                    
52 kurtosis_roll_dumbbell  character                  
53 kurtosis_picth_dumbbell character                  
54 kurtosis_yaw_dumbbell   character                  
55 skewness_roll_dumbbell  character                  
56 skewness_pitch_dumbbell character                  
57 skewness_yaw_dumbbell   character                  
58 max_yaw_dumbbell        character                  
59 min_yaw_dumbbell        character                  
60 amplitude_yaw_dumbbell  character                  
61 total_accel_dumbbell    integer                    
62 gyros_dumbbell_x        numeric                    
63 gyros_dumbbell_y        numeric                    
64 gyros_dumbbell_z        numeric                    
65 accel_dumbbell_x        integer                    
66 accel_dumbbell_y        integer                    
67 accel_dumbbell_z        integer                    
68 magnet_dumbbell_x       integer                    
69 magnet_dumbbell_y       integer                    
70 magnet_dumbbell_z       numeric                    
71 roll_forearm            numeric                    
72 pitch_forearm           numeric                    
73 yaw_forearm             numeric                    
74 kurtosis_roll_forearm   character                  
75 kurtosis_picth_forearm  character                  
76 kurtosis_yaw_forearm    character                  
77 skewness_roll_forearm   character                  
78 skewness_pitch_forearm  character                  
79 skewness_yaw_forearm    character                  
80 max_yaw_forearm         character                  
81 min_yaw_forearm         character                  
82 amplitude_yaw_forearm   character                  
83 total_accel_forearm     integer                    
84 gyros_forearm_x         numeric                    
85 gyros_forearm_y         numeric                    
86 gyros_forearm_z         numeric                    
87 accel_forearm_x         integer                    
88 accel_forearm_y         integer                    
89 accel_forearm_z         integer                    
90 magnet_forearm_x        integer                    
91 magnet_forearm_y        numeric                    
92 magnet_forearm_z        numeric                    
93 classe                  factor                     
> des(testing.as)
 
 No. of observations =  20 
   Variable                Class           Description
1  X                       integer                    
2  user_name               character                  
3  raw_timestamp_part_1    integer                    
4  raw_timestamp_part_2    integer                    
5  cvtd_timestamp          character                  
6  new_window              character                  
7  num_window              integer                    
8  roll_belt               numeric                    
9  pitch_belt              numeric                    
10 yaw_belt                numeric                    
11 total_accel_belt        integer                    
12 kurtosis_roll_belt      logical                    
13 kurtosis_picth_belt     logical                    
14 kurtosis_yaw_belt       logical                    
15 skewness_roll_belt      logical                    
16 skewness_roll_belt.1    logical                    
17 skewness_yaw_belt       logical                    
18 max_yaw_belt            logical                    
19 min_yaw_belt            logical                    
20 amplitude_yaw_belt      logical                    
21 gyros_belt_x            numeric                    
22 gyros_belt_y            numeric                    
23 gyros_belt_z            numeric                    
24 accel_belt_x            integer                    
25 accel_belt_y            integer                    
26 accel_belt_z            integer                    
27 magnet_belt_x           integer                    
28 magnet_belt_y           integer                    
29 magnet_belt_z           integer                    
30 roll_arm                numeric                    
31 pitch_arm               numeric                    
32 yaw_arm                 numeric                    
33 total_accel_arm         integer                    
34 gyros_arm_x             numeric                    
35 gyros_arm_y             numeric                    
36 gyros_arm_z             numeric                    
37 accel_arm_x             integer                    
38 accel_arm_y             integer                    
39 accel_arm_z             integer                    
40 magnet_arm_x            integer                    
41 magnet_arm_y            integer                    
42 magnet_arm_z            integer                    
43 kurtosis_roll_arm       logical                    
44 kurtosis_picth_arm      logical                    
45 kurtosis_yaw_arm        logical                    
46 skewness_roll_arm       logical                    
47 skewness_pitch_arm      logical                    
48 skewness_yaw_arm        logical                    
49 roll_dumbbell           numeric                    
50 pitch_dumbbell          numeric                    
51 yaw_dumbbell            numeric                    
52 kurtosis_roll_dumbbell  logical                    
53 kurtosis_picth_dumbbell logical                    
54 kurtosis_yaw_dumbbell   logical                    
55 skewness_roll_dumbbell  logical                    
56 skewness_pitch_dumbbell logical                    
57 skewness_yaw_dumbbell   logical                    
58 max_yaw_dumbbell        logical                    
59 min_yaw_dumbbell        logical                    
60 amplitude_yaw_dumbbell  logical                    
61 total_accel_dumbbell    integer                    
62 gyros_dumbbell_x        numeric                    
63 gyros_dumbbell_y        numeric                    
64 gyros_dumbbell_z        numeric                    
65 accel_dumbbell_x        integer                    
66 accel_dumbbell_y        integer                    
67 accel_dumbbell_z        integer                    
68 magnet_dumbbell_x       integer                    
69 magnet_dumbbell_y       integer                    
70 magnet_dumbbell_z       integer                    
71 roll_forearm            numeric                    
72 pitch_forearm           numeric                    
73 yaw_forearm             numeric                    
74 kurtosis_roll_forearm   logical                    
75 kurtosis_picth_forearm  logical                    
76 kurtosis_yaw_forearm    logical                    
77 skewness_roll_forearm   logical                    
78 skewness_pitch_forearm  logical                    
79 skewness_yaw_forearm    logical                    
80 max_yaw_forearm         logical                    
81 min_yaw_forearm         logical                    
82 amplitude_yaw_forearm   logical                    
83 total_accel_forearm     integer                    
84 gyros_forearm_x         numeric                    
85 gyros_forearm_y         numeric                    
86 gyros_forearm_z         numeric                    
87 accel_forearm_x         integer                    
88 accel_forearm_y         integer                    
89 accel_forearm_z         integer                    
90 magnet_forearm_x        integer                    
91 magnet_forearm_y        integer                    
92 magnet_forearm_z        integer                    
93 problem_id              integer   


#========= re-assign data===============#                 
> newdata.training<-training.as
> newdata.test<-testing.as

#crosscheck missing values in training dataset ==#
> any(is.na(newdata.training))
[1] FALSE


#==remove first 7 columns as they are irrelevant===#

> newdata.training.1<-newdata.training[,-c(1:7)]
> colnames(newdata.training.1)
 [1] "roll_belt"               "pitch_belt"              "yaw_belt"                "total_accel_belt"        "kurtosis_roll_belt"      "kurtosis_picth_belt"    
 [7] "kurtosis_yaw_belt"       "skewness_roll_belt"      "skewness_roll_belt.1"    "skewness_yaw_belt"       "max_yaw_belt"            "min_yaw_belt"           
[13] "amplitude_yaw_belt"      "gyros_belt_x"            "gyros_belt_y"            "gyros_belt_z"            "accel_belt_x"            "accel_belt_y"           
[19] "accel_belt_z"            "magnet_belt_x"           "magnet_belt_y"           "magnet_belt_z"           "roll_arm"                "pitch_arm"              
[25] "yaw_arm"                 "total_accel_arm"         "gyros_arm_x"             "gyros_arm_y"             "gyros_arm_z"             "accel_arm_x"            
[31] "accel_arm_y"             "accel_arm_z"             "magnet_arm_x"            "magnet_arm_y"            "magnet_arm_z"            "kurtosis_roll_arm"      
[37] "kurtosis_picth_arm"      "kurtosis_yaw_arm"        "skewness_roll_arm"       "skewness_pitch_arm"      "skewness_yaw_arm"        "roll_dumbbell"          
[43] "pitch_dumbbell"          "yaw_dumbbell"            "kurtosis_roll_dumbbell"  "kurtosis_picth_dumbbell" "kurtosis_yaw_dumbbell"   "skewness_roll_dumbbell" 
[49] "skewness_pitch_dumbbell" "skewness_yaw_dumbbell"   "max_yaw_dumbbell"        "min_yaw_dumbbell"        "amplitude_yaw_dumbbell"  "total_accel_dumbbell"   
[55] "gyros_dumbbell_x"        "gyros_dumbbell_y"        "gyros_dumbbell_z"        "accel_dumbbell_x"        "accel_dumbbell_y"        "accel_dumbbell_z"       
[61] "magnet_dumbbell_x"       "magnet_dumbbell_y"       "magnet_dumbbell_z"       "roll_forearm"            "pitch_forearm"           "yaw_forearm"            
[67] "kurtosis_roll_forearm"   "kurtosis_picth_forearm"  "kurtosis_yaw_forearm"    "skewness_roll_forearm"   "skewness_pitch_forearm"  "skewness_yaw_forearm"   
[73] "max_yaw_forearm"         "min_yaw_forearm"         "amplitude_yaw_forearm"   "total_accel_forearm"     "gyros_forearm_x"         "gyros_forearm_y"        
[79] "gyros_forearm_z"         "accel_forearm_x"         "accel_forearm_y"         "accel_forearm_z"         "magnet_forearm_x"        "magnet_forearm_y"       
[85] "magnet_forearm_z"        "classe"                 
> newdata.test.1<-newdata.test[,-c(1:7)]
> colnames(newdata.test.1)
 [1] "roll_belt"               "pitch_belt"              "yaw_belt"                "total_accel_belt"        "kurtosis_roll_belt"      "kurtosis_picth_belt"    
 [7] "kurtosis_yaw_belt"       "skewness_roll_belt"      "skewness_roll_belt.1"    "skewness_yaw_belt"       "max_yaw_belt"            "min_yaw_belt"           
[13] "amplitude_yaw_belt"      "gyros_belt_x"            "gyros_belt_y"            "gyros_belt_z"            "accel_belt_x"            "accel_belt_y"           
[19] "accel_belt_z"            "magnet_belt_x"           "magnet_belt_y"           "magnet_belt_z"           "roll_arm"                "pitch_arm"              
[25] "yaw_arm"                 "total_accel_arm"         "gyros_arm_x"             "gyros_arm_y"             "gyros_arm_z"             "accel_arm_x"            
[31] "accel_arm_y"             "accel_arm_z"             "magnet_arm_x"            "magnet_arm_y"            "magnet_arm_z"            "kurtosis_roll_arm"      
[37] "kurtosis_picth_arm"      "kurtosis_yaw_arm"        "skewness_roll_arm"       "skewness_pitch_arm"      "skewness_yaw_arm"        "roll_dumbbell"          
[43] "pitch_dumbbell"          "yaw_dumbbell"            "kurtosis_roll_dumbbell"  "kurtosis_picth_dumbbell" "kurtosis_yaw_dumbbell"   "skewness_roll_dumbbell" 
[49] "skewness_pitch_dumbbell" "skewness_yaw_dumbbell"   "max_yaw_dumbbell"        "min_yaw_dumbbell"        "amplitude_yaw_dumbbell"  "total_accel_dumbbell"   
[55] "gyros_dumbbell_x"        "gyros_dumbbell_y"        "gyros_dumbbell_z"        "accel_dumbbell_x"        "accel_dumbbell_y"        "accel_dumbbell_z"       
[61] "magnet_dumbbell_x"       "magnet_dumbbell_y"       "magnet_dumbbell_z"       "roll_forearm"            "pitch_forearm"           "yaw_forearm"            
[67] "kurtosis_roll_forearm"   "kurtosis_picth_forearm"  "kurtosis_yaw_forearm"    "skewness_roll_forearm"   "skewness_pitch_forearm"  "skewness_yaw_forearm"   
[73] "max_yaw_forearm"         "min_yaw_forearm"         "amplitude_yaw_forearm"   "total_accel_forearm"     "gyros_forearm_x"         "gyros_forearm_y"        
[79] "gyros_forearm_z"         "accel_forearm_x"         "accel_forearm_y"         "accel_forearm_z"         "magnet_forearm_x"        "magnet_forearm_y"       
[85] "magnet_forearm_z"        "problem_id"             
> des(newdata.training.1)
 
 No. of observations =  19622 
   Variable                Class           Description
1  roll_belt               numeric                    
2  pitch_belt              numeric                    
3  yaw_belt                numeric                    
4  total_accel_belt        integer                    
5  kurtosis_roll_belt      character                  
6  kurtosis_picth_belt     character                  
7  kurtosis_yaw_belt       character                  
8  skewness_roll_belt      character                  
9  skewness_roll_belt.1    character                  
10 skewness_yaw_belt       character                  
11 max_yaw_belt            character                  
12 min_yaw_belt            character                  
13 amplitude_yaw_belt      character                  
14 gyros_belt_x            numeric                    
15 gyros_belt_y            numeric                    
16 gyros_belt_z            numeric                    
17 accel_belt_x            integer                    
18 accel_belt_y            integer                    
19 accel_belt_z            integer                    
20 magnet_belt_x           integer                    
21 magnet_belt_y           integer                    
22 magnet_belt_z           integer                    
23 roll_arm                numeric                    
24 pitch_arm               numeric                    
25 yaw_arm                 numeric                    
26 total_accel_arm         integer                    
27 gyros_arm_x             numeric                    
28 gyros_arm_y             numeric                    
29 gyros_arm_z             numeric                    
30 accel_arm_x             integer                    
31 accel_arm_y             integer                    
32 accel_arm_z             integer                    
33 magnet_arm_x            integer                    
34 magnet_arm_y            integer                    
35 magnet_arm_z            integer                    
36 kurtosis_roll_arm       character                  
37 kurtosis_picth_arm      character                  
38 kurtosis_yaw_arm        character                  
39 skewness_roll_arm       character                  
40 skewness_pitch_arm      character                  
41 skewness_yaw_arm        character                  
42 roll_dumbbell           numeric                    
43 pitch_dumbbell          numeric                    
44 yaw_dumbbell            numeric                    
45 kurtosis_roll_dumbbell  character                  
46 kurtosis_picth_dumbbell character                  
47 kurtosis_yaw_dumbbell   character                  
48 skewness_roll_dumbbell  character                  
49 skewness_pitch_dumbbell character                  
50 skewness_yaw_dumbbell   character                  
51 max_yaw_dumbbell        character                  
52 min_yaw_dumbbell        character                  
53 amplitude_yaw_dumbbell  character                  
54 total_accel_dumbbell    integer                    
55 gyros_dumbbell_x        numeric                    
56 gyros_dumbbell_y        numeric                    
57 gyros_dumbbell_z        numeric                    
58 accel_dumbbell_x        integer                    
59 accel_dumbbell_y        integer                    
60 accel_dumbbell_z        integer                    
61 magnet_dumbbell_x       integer                    
62 magnet_dumbbell_y       integer                    
63 magnet_dumbbell_z       numeric                    
64 roll_forearm            numeric                    
65 pitch_forearm           numeric                    
66 yaw_forearm             numeric                    
67 kurtosis_roll_forearm   character                  
68 kurtosis_picth_forearm  character                  
69 kurtosis_yaw_forearm    character                  
70 skewness_roll_forearm   character                  
71 skewness_pitch_forearm  character                  
72 skewness_yaw_forearm    character                  
73 max_yaw_forearm         character                  
74 min_yaw_forearm         character                  
75 amplitude_yaw_forearm   character                  
76 total_accel_forearm     integer                    
77 gyros_forearm_x         numeric                    
78 gyros_forearm_y         numeric                    
79 gyros_forearm_z         numeric                    
80 accel_forearm_x         integer                    
81 accel_forearm_y         integer                    
82 accel_forearm_z         integer                    
83 magnet_forearm_x        integer                    
84 magnet_forearm_y        numeric                    
85 magnet_forearm_z        numeric                    
86 classe                  factor 


#==consider removing other irrelevant data such as those with variance, kurtosis, skweness and average values===========#                   
> newdata.training.1<-newdata.training.1[,-c(5:13,36:41,45:53,67:75)]
> newdata.test.1<-newdata.test.1[,-c(5:13,36:41,45:53,67:75)]
> des(newdata.training.1) #
 
 No. of observations =  19622 
   Variable             Class           Description
1  roll_belt            numeric                    
2  pitch_belt           numeric                    
3  yaw_belt             numeric                    
4  total_accel_belt     integer                    
5  gyros_belt_x         numeric                    
6  gyros_belt_y         numeric                    
7  gyros_belt_z         numeric                    
8  accel_belt_x         integer                    
9  accel_belt_y         integer                    
10 accel_belt_z         integer                    
11 magnet_belt_x        integer                    
12 magnet_belt_y        integer                    
13 magnet_belt_z        integer                    
14 roll_arm             numeric                    
15 pitch_arm            numeric                    
16 yaw_arm              numeric                    
17 total_accel_arm      integer                    
18 gyros_arm_x          numeric                    
19 gyros_arm_y          numeric                    
20 gyros_arm_z          numeric                    
21 accel_arm_x          integer                    
22 accel_arm_y          integer                    
23 accel_arm_z          integer                    
24 magnet_arm_x         integer                    
25 magnet_arm_y         integer                    
26 magnet_arm_z         integer                    
27 roll_dumbbell        numeric                    
28 pitch_dumbbell       numeric                    
29 yaw_dumbbell         numeric                    
30 total_accel_dumbbell integer                    
31 gyros_dumbbell_x     numeric                    
32 gyros_dumbbell_y     numeric                    
33 gyros_dumbbell_z     numeric                    
34 accel_dumbbell_x     integer                    
35 accel_dumbbell_y     integer                    
36 accel_dumbbell_z     integer                    
37 magnet_dumbbell_x    integer                    
38 magnet_dumbbell_y    integer                    
39 magnet_dumbbell_z    numeric                    
40 roll_forearm         numeric                    
41 pitch_forearm        numeric                    
42 yaw_forearm          numeric                    
43 total_accel_forearm  integer                    
44 gyros_forearm_x      numeric                    
45 gyros_forearm_y      numeric                    
46 gyros_forearm_z      numeric                    
47 accel_forearm_x      integer                    
48 accel_forearm_y      integer                    
49 accel_forearm_z      integer                    
50 magnet_forearm_x     integer                    
51 magnet_forearm_y     numeric                    
52 magnet_forearm_z     numeric                    
53 classe               factor                     
> des(newdata.test.1)
 
 No. of observations =  20 
   Variable             Class           Description
1  roll_belt            numeric                    
2  pitch_belt           numeric                    
3  yaw_belt             numeric                    
4  total_accel_belt     integer                    
5  gyros_belt_x         numeric                    
6  gyros_belt_y         numeric                    
7  gyros_belt_z         numeric                    
8  accel_belt_x         integer                    
9  accel_belt_y         integer                    
10 accel_belt_z         integer                    
11 magnet_belt_x        integer                    
12 magnet_belt_y        integer                    
13 magnet_belt_z        integer                    
14 roll_arm             numeric                    
15 pitch_arm            numeric                    
16 yaw_arm              numeric                    
17 total_accel_arm      integer                    
18 gyros_arm_x          numeric                    
19 gyros_arm_y          numeric                    
20 gyros_arm_z          numeric                    
21 accel_arm_x          integer                    
22 accel_arm_y          integer                    
23 accel_arm_z          integer                    
24 magnet_arm_x         integer                    
25 magnet_arm_y         integer                    
26 magnet_arm_z         integer                    
27 roll_dumbbell        numeric                    
28 pitch_dumbbell       numeric                    
29 yaw_dumbbell         numeric                    
30 total_accel_dumbbell integer                    
31 gyros_dumbbell_x     numeric                    
32 gyros_dumbbell_y     numeric                    
33 gyros_dumbbell_z     numeric                    
34 accel_dumbbell_x     integer                    
35 accel_dumbbell_y     integer                    
36 accel_dumbbell_z     integer                    
37 magnet_dumbbell_x    integer                    
38 magnet_dumbbell_y    integer                    
39 magnet_dumbbell_z    integer                    
40 roll_forearm         numeric                    
41 pitch_forearm        numeric                    
42 yaw_forearm          numeric                    
43 total_accel_forearm  integer                    
44 gyros_forearm_x      numeric                    
45 gyros_forearm_y      numeric                    
46 gyros_forearm_z      numeric                    
47 accel_forearm_x      integer                    
48 accel_forearm_y      integer                    
49 accel_forearm_z      integer                    
50 magnet_forearm_x     integer                    
51 magnet_forearm_y     integer                    
52 magnet_forearm_z     integer                    
53 problem_id           integer  


#==Question 2: How you used cross validation

#===========================Algoriths for processing data============================#
#The dataset is very huge and could make working difficult. Many coursera students expressed worry over this.
#Take subsets of data in order to reduce runtime as recommended on cousera discussion==============#


# First. Take 30% of training set                 
> set.seed(200)
> dataset.1 <- createDataPartition(y=newdata.training.1$classe, p=0.30, list=FALSE)
> df_set.1 <- newdata.training.1[dataset.1,] # 30% available
> df_rest.1 <- newdata.training.1[-dataset.1,] #70% available  in remainder

# Dataset 2. Take 40% of training dataset
> set.seed(200)
> dataset.2 <- createDataPartition(y=df_rest.1$classe, p=.40, list=FALSE) #split the rest of data into  40% vrs 60%
> df_set.2 <- df_rest.1[dataset.2,]   # 40%
> df_rest.2 <-df_rest.1[-dataset.2,]  #60%


# Dataset 3 take 50% of training set
> set.seed(200)
> dataset.3 <- createDataPartition(y=df_rest.2$classe, p=0.50, list=FALSE) #split the rest into  # 50% vrs 50%
> df_set.3 <-df_rest.2[dataset.3,]  # 50% of data  into 3


# Dataset 4: Use remainder of training dataset as fourth dataset
> df_set.4 <-df_rest.2[-dataset.3,] # 50% of data into 4
> set.seed(200)
> inTrain <- createDataPartition(y=df_set.1$classe, p=0.6, list=FALSE)     # train first 30% of data into training 1 and test 1
> df_set.1.training <- df_set.1[inTrain,]
> df_set.1.testing <- df_set.1[-inTrain,]
> des(df_set.1.training)
 
 No. of observations =  3535 
   Variable             Class           Description
1  roll_belt            numeric                    
2  pitch_belt           numeric                    
3  yaw_belt             numeric                    
4  total_accel_belt     integer                    
5  gyros_belt_x         numeric                    
6  gyros_belt_y         numeric                    
7  gyros_belt_z         numeric                    
8  accel_belt_x         integer                    
9  accel_belt_y         integer                    
10 accel_belt_z         integer                    
11 magnet_belt_x        integer                    
12 magnet_belt_y        integer                    
13 magnet_belt_z        integer                    
14 roll_arm             numeric                    
15 pitch_arm            numeric                    
16 yaw_arm              numeric                    
17 total_accel_arm      integer                    
18 gyros_arm_x          numeric                    
19 gyros_arm_y          numeric                    
20 gyros_arm_z          numeric                    
21 accel_arm_x          integer                    
22 accel_arm_y          integer                    
23 accel_arm_z          integer                    
24 magnet_arm_x         integer                    
25 magnet_arm_y         integer                    
26 magnet_arm_z         integer                    
27 roll_dumbbell        numeric                    
28 pitch_dumbbell       numeric                    
29 yaw_dumbbell         numeric                    
30 total_accel_dumbbell integer                    
31 gyros_dumbbell_x     numeric                    
32 gyros_dumbbell_y     numeric                    
33 gyros_dumbbell_z     numeric                    
34 accel_dumbbell_x     integer                    
35 accel_dumbbell_y     integer                    
36 accel_dumbbell_z     integer                    
37 magnet_dumbbell_x    integer                    
38 magnet_dumbbell_y    integer                    
39 magnet_dumbbell_z    numeric                    
40 roll_forearm         numeric                    
41 pitch_forearm        numeric                    
42 yaw_forearm          numeric                    
43 total_accel_forearm  integer                    
44 gyros_forearm_x      numeric                    
45 gyros_forearm_y      numeric                    
46 gyros_forearm_z      numeric                    
47 accel_forearm_x      integer                    
48 accel_forearm_y      integer                    
49 accel_forearm_z      integer                    
50 magnet_forearm_x     integer                    
51 magnet_forearm_y     numeric                    
52 magnet_forearm_z     numeric                    
53 classe               factor  


# Model evaluation and predictions on data sets using cross validations
#Divide each of the dataset into training (60%) and testing (40%)
#============= Data set 1========================================#
                  
> df_set.1.training$classe<-factor(df_set.1.training$classe) # convert classe to factor variable
> set.seed(200)
> modFit.1 <- train(df_set.1.training$classe ~ ., method="rf", trControl=trainControl(method = "cv",
+ number = 4), data=df_set.1.training)
Loading required package: class

Attaching package: ‘e1071’

The following object is masked from ‘package:Hmisc’:

    impute

> print(modFit.1, digits=3)
Random Forest 

3535 samples
  52 predictors
   5 classes: 'A', 'B', 'C', 'D', 'E' 

No pre-processing
Resampling: Cross-Validated (4 fold) 

Summary of sample sizes: 2651, 2652, 2649, 2653 

Resampling results across tuning parameters:

  mtry  Accuracy  Kappa  Accuracy SD  Kappa SD
  2     0.951     0.938  0.00889      0.0113  
  27    0.96      0.95   0.0114       0.0144  
  52    0.954     0.942  0.0127       0.0161  

Accuracy was used to select the optimal model using  the largest value.
The final value used for the model was mtry = 27. 


# make prediction on test data in first sample and assess accuracy

> prediction <- predict(modFit.1, newdata=df_set.1.testing)

# Print and take note of the accuracy value and out of sample error

> print(confusionMatrix(prediction, df_set.1.testing$classe))
Confusion Matrix and Statistics

          Reference
Prediction   A   B   C   D   E
         A 666  14   0   0   0
         B   2 433   9   3   1
         C   0   6 398  11   3
         D   0   1   3 372   3
         E   1   2   0   0 426

Overall Statistics
                                          
               Accuracy : 0.9749          
                 95% CI : (0.9678, 0.9809)
    No Information Rate : 0.2842          
    P-Value [Acc > NIR] : < 2.2e-16       
                                          
                  Kappa : 0.9683          
 Mcnemar's Test P-Value : NA              

Statistics by Class:

                     Class: A Class: B Class: C Class: D Class: E
Sensitivity            0.9955   0.9496   0.9707   0.9637   0.9838
Specificity            0.9917   0.9921   0.9897   0.9964   0.9984
Pos Pred Value         0.9794   0.9665   0.9522   0.9815   0.9930
Neg Pred Value         0.9982   0.9879   0.9938   0.9929   0.9964
Prevalence             0.2842   0.1937   0.1742   0.1640   0.1839
Detection Rate         0.2829   0.1839   0.1691   0.1580   0.1810
Detection Prevalence   0.2889   0.1903   0.1776   0.1610   0.1822
Balanced Accuracy      0.9936   0.9708   0.9802   0.9801   0.9911



#Run against 20 testing data provided in assignment and take note of prediction output

> print(predict(modFit.1, newdata=newdata.test.1))
 [1] B A B A A E D D A A B C B A E E A B B B
Levels: A B C D E



#=================== Dataset 2======================================================================================#

> set.seed(200)
> inTrain <- createDataPartition(y=df_set.2$classe, p=0.6, list=FALSE)  # train second 40% of data
> df_set.2.training<- df_set.2[inTrain,]
> df_set.2.testing <- df_set.2[-inTrain,]
> des(df_set.2.training)
 
 No. of observations =  3298 
   Variable             Class           Description
1  roll_belt            numeric                    
2  pitch_belt           numeric                    
3  yaw_belt             numeric                    
4  total_accel_belt     integer                    
5  gyros_belt_x         numeric                    
6  gyros_belt_y         numeric                    
7  gyros_belt_z         numeric                    
8  accel_belt_x         integer                    
9  accel_belt_y         integer                    
10 accel_belt_z         integer                    
11 magnet_belt_x        integer                    
12 magnet_belt_y        integer                    
13 magnet_belt_z        integer                    
14 roll_arm             numeric                    
15 pitch_arm            numeric                    
16 yaw_arm              numeric                    
17 total_accel_arm      integer                    
18 gyros_arm_x          numeric                    
19 gyros_arm_y          numeric                    
20 gyros_arm_z          numeric                    
21 accel_arm_x          integer                    
22 accel_arm_y          integer                    
23 accel_arm_z          integer                    
24 magnet_arm_x         integer                    
25 magnet_arm_y         integer                    
26 magnet_arm_z         integer                    
27 roll_dumbbell        numeric                    
28 pitch_dumbbell       numeric                    
29 yaw_dumbbell         numeric                    
30 total_accel_dumbbell integer                    
31 gyros_dumbbell_x     numeric                    
32 gyros_dumbbell_y     numeric                    
33 gyros_dumbbell_z     numeric                    
34 accel_dumbbell_x     integer                    
35 accel_dumbbell_y     integer                    
36 accel_dumbbell_z     integer                    
37 magnet_dumbbell_x    integer                    
38 magnet_dumbbell_y    integer                    
39 magnet_dumbbell_z    numeric                    
40 roll_forearm         numeric                    
41 pitch_forearm        numeric                    
42 yaw_forearm          numeric                    
43 total_accel_forearm  integer                    
44 gyros_forearm_x      numeric                    
45 gyros_forearm_y      numeric                    
46 gyros_forearm_z      numeric                    
47 accel_forearm_x      integer                    
48 accel_forearm_y      integer                    
49 accel_forearm_z      integer                    
50 magnet_forearm_x     integer                    
51 magnet_forearm_y     numeric                    
52 magnet_forearm_z     numeric                    
53 classe               factor  

# make prediction on test data in second sample and assess accuracy
                 
> df_set.2.training$classe<-factor(df_set.2.training$classe)
> modFit.2 <- train(df_set.2.training$classe ~ ., method="rf", trControl=trainControl(method = "cv",
+ number = 4), data=df_set.2.training)
> print(modFit.2, digits=3)
Random Forest 

3298 samples
  52 predictors
   5 classes: 'A', 'B', 'C', 'D', 'E' 

No pre-processing
Resampling: Cross-Validated (4 fold) 

Summary of sample sizes: 2474, 2475, 2474, 2471 

Resampling results across tuning parameters:

  mtry  Accuracy  Kappa  Accuracy SD  Kappa SD
  2     0.952     0.939  0.00208      0.00261 
  27    0.956     0.944  0.00287      0.00364 
  52    0.952     0.939  0.00618      0.00782 

Accuracy was used to select the optimal model using  the largest value.
The final value used for the model was mtry = 27. 
> prediction <- predict(modFit.2, newdata=df_set.2.testing)
> print(confusionMatrix(prediction, df_set.2.testing$classe))
Confusion Matrix and Statistics

          Reference
Prediction   A   B   C   D   E
         A 616  13   0   0   0
         B   7 401  17   1   5
         C   2   8 358   9   1
         D   0   1   5 350   3
         E   0   2   3   0 395

Overall Statistics
                                          
               Accuracy : 0.965           
                 95% CI : (0.9564, 0.9722)
    No Information Rate : 0.2845          
    P-Value [Acc > NIR] : < 2.2e-16       
                                          
                  Kappa : 0.9557          
 Mcnemar's Test P-Value : NA              

Statistics by Class:

                     Class: A Class: B Class: C Class: D Class: E
Sensitivity            0.9856   0.9435   0.9347   0.9722   0.9777
Specificity            0.9917   0.9831   0.9890   0.9951   0.9972
Pos Pred Value         0.9793   0.9304   0.9471   0.9749   0.9875
Neg Pred Value         0.9943   0.9864   0.9863   0.9946   0.9950
Prevalence             0.2845   0.1934   0.1743   0.1639   0.1839
Detection Rate         0.2804   0.1825   0.1629   0.1593   0.1798
Detection Prevalence   0.2863   0.1962   0.1721   0.1634   0.1821
Balanced Accuracy      0.9887   0.9633   0.9619   0.9837   0.9875

# Run against 20 testing provided in assignment

> print(predict(modFit.2, newdata=newdata.test.1))
 [1] B A B A A E D B A A B C B A E E A B B B
Levels: A B C D E

#===================== dataset 3===============================================#
# Train third  50% of data into training 3 and test 3 datasets

> set.seed(200)
> inTrain <- createDataPartition(y=df_set.3$classe, p=0.6, list=FALSE) # train third  50% of data into training 3 and test 3
> df_set.3.training <- df_set.3[inTrain,]
> df_set.3.testing <- df_set.3[-inTrain,]
> df_set.3.training$classe<-factor(df_set.3.training$classe)
> modFit.3 <- train(df_set.2.training$classe ~ ., method="rf", trControl=trainControl(method = "cv",
+ number = 4), data=df_set.2.training)
> print(modFit.3, digits=3)
Random Forest 

3298 samples
  52 predictors
   5 classes: 'A', 'B', 'C', 'D', 'E' 

No pre-processing
Resampling: Cross-Validated (4 fold) 

Summary of sample sizes: 2475, 2473, 2473, 2473 

Resampling results across tuning parameters:

  mtry  Accuracy  Kappa  Accuracy SD  Kappa SD
  2     0.949     0.936  0.0042       0.00532 
  27    0.957     0.946  0.00303      0.00386 
  52    0.954     0.942  0.00299      0.00375 

Accuracy was used to select the optimal model using  the largest value.


#==============make prediction on test data 3 and assess accuracy=================#

The final value used for the model was mtry = 27. 
> prediction <- predict(modFit.3, newdata=df_set.3.testing)
> print(confusionMatrix(prediction, df_set.3.testing$classe))
Confusion Matrix and Statistics

          Reference
Prediction   A   B   C   D   E
         A 463   7   0   0   0
         B   4 303  12   1   0
         C   0   5 268   7   1
         D   1   3   7 262   3
         E   0   0   0   0 298

Overall Statistics
                                          
               Accuracy : 0.969           
                 95% CI : (0.9594, 0.9768)
    No Information Rate : 0.2845          
    P-Value [Acc > NIR] : < 2.2e-16       
                                          
                  Kappa : 0.9608          
 Mcnemar's Test P-Value : NA              

Statistics by Class:

                     Class: A Class: B Class: C Class: D Class: E
Sensitivity            0.9893   0.9528   0.9338   0.9704   0.9868
Specificity            0.9941   0.9872   0.9904   0.9898   1.0000
Pos Pred Value         0.9851   0.9469   0.9537   0.9493   1.0000
Neg Pred Value         0.9957   0.9887   0.9861   0.9942   0.9970
Prevalence             0.2845   0.1933   0.1745   0.1641   0.1836
Detection Rate         0.2815   0.1842   0.1629   0.1593   0.1812
Detection Prevalence   0.2857   0.1945   0.1708   0.1678   0.1812
Balanced Accuracy      0.9917   0.9700   0.9621   0.9801   0.9934


# Run against 20 testing provided in assignment

> print(predict(modFit.3, newdata=newdata.test.1))
 [1] B A B A A E D B A A B C B A E E A B B B
Levels: A B C D E

#=================== Dataset 4=========================================================#
Train 50% of data into training 4 and test 4

> set.seed(200)
> inTrain <- createDataPartition(y=df_set.4$classe, p=0.6, list=FALSE) # train 50% of data into training 4 and test 4
> df_set.4.training <- df_set.4[inTrain,]
> df_set.4.testing <- df_set.4[-inTrain,]
> df_set.4.training$classe<-factor(df_set.4.training$classe)

#Perform random forest on dataset centre and scale to get a best variance in the model

> modFit.4 <- train(df_set.4.training$classe ~ ., method="rf", trControl=trainControl(method = "cv",
+ number = 4), data=df_set.4.training)
> print(modFit.4)
Random Forest 

2473 samples
  52 predictors
   5 classes: 'A', 'B', 'C', 'D', 'E' 

No pre-processing
Resampling: Cross-Validated (4 fold) 

Summary of sample sizes: 1855, 1855, 1853, 1856 

Resampling results across tuning parameters:

  mtry  Accuracy  Kappa  Accuracy SD  Kappa SD
  2     0.94      0.924  0.00661      0.00839 
  27    0.945     0.931  0.0119       0.0151  
  52    0.936     0.919  0.00547      0.00695 

Accuracy was used to select the optimal model using  the largest value.
The final value used for the model was mtry = 27. 


#make prediction on test data

> predictions <- predict(modFit.4, newdata=df_set.4.testing)
> print(confusionMatrix(predictions, df_set.4.testing$classe))
Confusion Matrix and Statistics

          Reference
Prediction   A   B   C   D   E
         A 467  12   0   1   0
         B   1 298   7   2   4
         C   0   7 274  11   5
         D   0   1   6 255   1
         E   0   0   0   1 292

Overall Statistics
                                         
               Accuracy : 0.9641         
                 95% CI : (0.954, 0.9726)
    No Information Rate : 0.2845         
    P-Value [Acc > NIR] : < 2.2e-16      
                                         
                  Kappa : 0.9546         
 Mcnemar's Test P-Value : NA             

Statistics by Class:

                     Class: A Class: B Class: C Class: D Class: E
Sensitivity            0.9979   0.9371   0.9547   0.9444   0.9669
Specificity            0.9890   0.9894   0.9831   0.9942   0.9993
Pos Pred Value         0.9729   0.9551   0.9226   0.9696   0.9966
Neg Pred Value         0.9991   0.9850   0.9904   0.9891   0.9926
Prevalence             0.2845   0.1933   0.1745   0.1641   0.1836
Detection Rate         0.2839   0.1812   0.1666   0.1550   0.1775
Detection Prevalence   0.2918   0.1897   0.1805   0.1599   0.1781
Balanced Accuracy      0.9934   0.9633   0.9689   0.9693   0.9831


# Run against 20 testing provided in assignment
> print(predict(modFit.4, newdata=newdata.test.1))
 [1] B A B A A E D D A A B C B A E E A B B B
Levels: A B C D E

# Question 3: What you think the expected out of sample error
#In week 1 of coursera course. An out of sample error was define the error one gets on a new data
# This will be 1 - (accuracy values for each training prediction on the  testing dataset created)

# Testing set 1 predictions =  1 - 0.9749  =  0.0251
# Testing set 2 predictions =   1 - 0.9663 = 0.0337
#Testing set 3 predictions  =  1- 0.969    = 0.031
#Testing set 4 predictions  =  1 - 0.9623  = 0.031

#Average out of sample error  =  0.0302

#Final Predictions on new dataset with 20 observations is the same for all predictions
# Prediction   B A B A A E D D A A B C B A E E A B B B



