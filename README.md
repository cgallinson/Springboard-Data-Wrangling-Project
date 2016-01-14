# Springboard-Data-Wrangling-Project
Data wrangling exercise as part of springboard.com "Foundations of Data Science" course.
#
# Import files
subject_train <- read.table("~/Downloads/UCI HAR Dataset/train/subject_train.txt", quote="\"", comment.char="")
y_train <- read.table("~/Downloads/UCI HAR Dataset/train/y_train.txt", quote="\"", comment.char="")
X_train <- read.table("~/Downloads/UCI HAR Dataset/train/X_train.txt", quote="\"", comment.char="")
subject_test <- read.table("~/Downloads/UCI HAR Dataset/test/subject_test.txt", quote="\"", comment.char="")
y_test <- read.table("~/Downloads/UCI HAR Dataset/test/y_test.txt", quote="\"", comment.char="")
X_test <- read.table("~/Downloads/UCI HAR Dataset/test/X_test.txt", quote="\"", comment.char="")
#
# Create local dfs from data files
a1 <- tbl_df(subject_train)
a2 <- tbl_df(y_train)
a3 <- tbl_df(X_train)
b1 <- tbl_df(subject_test)
b2 <- tbl_df(y_test)
b3 <- tbl_df(X_test)

# Merge data from training and test subject, activity and data files
subject <- bind_rows(a1, b1)
activity <- bind_rows(a2, b2)
data <- bind_rows(a3, b3)
#
# Rename variable columns
names(subject) <- c("subject")
names(activity) <- c("activity")
#
# Calculate average for each measurement
data_means <- rowMeans(data)
#
# Convert to local data frame
data_means <- data_frame(data_means)
data_means <- tbl_df(data_means)
#
# Rename this column of mean variables
names(data_means) <- c("mean values")

# Convert "data" tbl_df to matrix
data <- data.matrix(data)
#
# Calculate standard deviation for each measurement
data_sds <- rowSds(data)
#
# Convert to local data frame
data_sds <- data_frame(data_sds)
data_sds <- tbl_df(data_sds)
#
# Rename this column of std variables
names(data_sds) <- c("sd values")
#
# Merge subject, activity, mean and std columns
data_table <- bind_cols(subject, activity, data_means, data_sds)
#
# Group by activity and obtain mean means and stds for each activity
activity_summary <- data_table  %>% group_by(activity)  %>% summarise_each(funs(mean))
#
# Group by subject and activity and obtain mean means and stds for each subject and each activity
activity_summary <- select(activity_summary, -subject)
activitybysubject_summary <- data_table  %>% group_by(subject, activity)  %>% summarise_each(funs(mean))

