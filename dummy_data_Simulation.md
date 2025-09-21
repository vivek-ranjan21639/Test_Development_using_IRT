## Sample dataset — simulated pilot responses

We will simulate N = 800 examinees responding to 40 items under a 3PL model. The simulation includes a plausible spread of item parameters and stores both the response matrix and the "true" ability used for simulation (for validation).

**Files produced:**

* `data/pilot_responses.csv` (id + Q1...Q40)



---

## R code — simulate pilot dataset (`scripts/01_simulate_pilot.R`)

```r

# Simulate pilot responses for 40 items and N examinees under a 3PL model

if(!require('readr')) install.packages('readr')

library(readr)

set.seed(2025)
N <- 800
J <- 40

# Simulate true abilities
theta <- rnorm(N, 0, 1)

# Create plausible item parameters
# a: discrimination (0.4 to 2.8), b: difficulty (-2 to +2), c: guessing (0.15 to 0.30)
a_vals <- runif(J, 0.4, 2.8)
b_vals <- seq(-2, 2, length.out = J) + rnorm(J, 0, 0.3)
c_vals <- runif(J, 0.15, 0.30)

# Generate response probabilities and simulate responses
resp <- matrix(0, nrow = N, ncol = J)
for(i in 1:N){
  for(j in 1:J){
    p <- c_vals[j] + (1 - c_vals[j]) / (1 + exp(-a_vals[j] * (theta[i] - b_vals[j])))
    resp[i,j] <- rbinom(1, 1, p)
  }
}

# Save data
colnames(resp) <- paste0('Q', 1:J)
pilot_df <- as.data.frame(resp)
pilot_df <- cbind(Examinee_ID = 1:N, pilot_df)

write_csv(pilot_df, 'pilot_responses.csv')

```

