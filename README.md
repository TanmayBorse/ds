	FCFS RR SJF
	
	#include <stdio.h>
	#include<limits.h>
	typedef struct {
	    	int pid;
	    	int burst;
	    	int arrival;
	    	int priority;
	    	int waiting;
	    	int turnaround;
	    	int remaining;
	    	int completed;
	} Process;
	
	void calculateAvgTimes(Process proc[], int n) {
	    	float total_waiting = 0, total_turnaround = 0;
	    	for (int i = 0; i < n; i++) {
	        	total_waiting += proc[i].waiting;
	        	total_turnaround += proc[i].turnaround;
	    	}
	    	printf("Average Waiting Time: %.2f\n", total_waiting / n);
	    	printf("Average Turnaround Time: %.2f\n", total_turnaround / n);
	}
	
	void printProcesses(Process proc[], int n) {
	    	printf("PID\tBurst\tArrival\tPriority\tWaiting\tTurnaround\n");
	    	for (int i = 0; i < n; i++) {
	        	printf("%d\t%d\t%d\t%d\t\t%d\t%d\n", proc[i].pid, proc[i].burst, proc[i].arrival,
					proc[i].priority, proc[i].waiting, proc[i].turnaround);
	    	}
	}
	
	void fcfsScheduling(Process proc[], int n) {
	    	int time = 0;
	    	proc[0].waiting = 0;
	    	proc[0].turnaround = proc[0].burst;
	
	    	for (int i = 1; i < n; i++) {
	        	time += proc[i-1].burst;
	        	proc[i].waiting = time - proc[i].arrival;
	        	proc[i].turnaround = proc[i].waiting + proc[i].burst;
	    	}
	
	    	printf("\nFCFS Scheduling:\n");
	    	printProcesses(proc, n);
	    	calculateAvgTimes(proc, n);
	}
	
	void sjfScheduling(Process proc[], int n) {
	    	int completed = 0, time = 0, min_burst = INT_MAX, shortest = 0, finish_time;
	    	int completed_flag = 0;
	
	    	while (completed != n) {
	        	for (int i = 0; i < n; i++) {
	            		if (proc[i].arrival <= time && !proc[i].completed && proc[i].burst < min_burst) {
	                	min_burst = proc[i].burst;
	                	shortest = i;
	                	completed_flag = 1;
	            		}
	        	}
	
	        	if (!completed_flag) {
	            	time++;
	            	continue;
	        	}
	
	        	time += proc[shortest].burst;
	        	proc[shortest].waiting = time - proc[shortest].arrival - proc[shortest].burst;
	        	proc[shortest].turnaround = time - proc[shortest].arrival;
	        	proc[shortest].completed = 1;
	        	min_burst = INT_MAX;
	        	completed++;
	        	completed_flag = 0;
	    	}
	
	    	printf("\nSJF Scheduling:\n");
	    	printProcesses(proc, n);
	    	calculateAvgTimes(proc, n);
	}
	
	void srtfScheduling(Process proc[], int n) {
	    	int completed = 0, time = 0, min_remaining = INT_MAX, shortest = 0;
	    	int completed_flag = 0, finish_time;
	    
	    	while (completed != n) {
	        	for (int i = 0; i < n; i++) {
	            		if (proc[i].arrival <= time && proc[i].remaining > 0 && proc[i].remaining < min_remaining) {
	                	min_remaining = proc[i].remaining;
	                	shortest = i;
	                	completed_flag = 1;
	            		}
	        	}
	
	        	if (!completed_flag) {
	            	time++;
	            	continue;
	        	}
	
	        	proc[shortest].remaining--;
	        	min_remaining = proc[shortest].remaining;
	
	        	if (proc[shortest].remaining == 0) {
	            	completed++;
	            	completed_flag = 0;
	            	finish_time = time + 1;
	            	proc[shortest].waiting = finish_time - proc[shortest].burst - proc[shortest].arrival;
	            	proc[shortest].turnaround = finish_time - proc[shortest].arrival;
	            	min_remaining = INT_MAX;
	        	}
	
	        	time++;
	    	}
	
	    	printf("\nSRTF Scheduling:\n");
	    	printProcesses(proc, n);
	    	calculateAvgTimes(proc, n);
	}
	
	void priorityScheduling(Process proc[], int n) {
	    	int completed = 0, time = 0, max_priority = INT_MAX, highest = 0;
	    	int completed_flag = 0;
	
	    	while (completed != n) {
	        	for (int i = 0; i < n; i++) {
	            		if (proc[i].arrival <= time && !proc[i].completed && proc[i].priority < max_priority) {
	                		max_priority = proc[i].priority;
	                		highest = i;
	                		completed_flag = 1;
	            		}
	        	}
	
	        	if (!completed_flag) {
	            	time++;
	            	continue;
	        	}
	
	        	time += proc[highest].burst;
	        	proc[highest].waiting = time - proc[highest].arrival - proc[highest].burst;
	        	proc[highest].turnaround = time - proc[highest].arrival;
	        	proc[highest].completed = 1;
	        	max_priority = INT_MAX;
	        	completed++;
	        	completed_flag = 0;
	    	}
	
	    	printf("\nPriority Scheduling:\n");
	    	printProcesses(proc, n);
	    	calculateAvgTimes(proc, n);
	}
	void roundRobinScheduling(Process proc[], int n, int quantum) {
	    	int time = 0, completed = 0;
	    	int total_waiting = 0, total_turnaround = 0;
	    
	    	while (completed != n) {
	        	int done = 1;
	        	for (int i = 0; i < n; i++) {
	            		if (proc[i].remaining > 0) {
	                		done = 0;
	                		if (proc[i].remaining > quantum && proc[i].arrival <= time) {
	                    			time += quantum;
	                    			proc[i].remaining -= quantum;
	                		} 
					else if (proc[i].arrival <= time) {
	                    			time += proc[i].remaining;
	                    			proc[i].waiting = time - proc[i].arrival - proc[i].burst;
	                    			proc[i].turnaround = time - proc[i].arrival;
	                    			proc[i].remaining = 0;
	                    			completed++;
	                		}
	            		}
	        	}
	        	if (done) break;
	    	}
	
	    	printf("\nRound Robin Scheduling:\n");
	    	printProcesses(proc, n);
	    	calculateAvgTimes(proc, n);
	}
	
	int main() {
	    	int n, quantum, choice;
	    	printf("Enter the number of processes: ");
	    	scanf("%d", &n);
		Process proc[n];
	
	    	for (int i = 0; i < n; i++) {
	        	proc[i].pid = i + 1;
	        	printf("Enter burst time, arrival time, and priority for process %d:\n", proc[i].pid);
	        	scanf("%d %d %d", &proc[i].burst, &proc[i].arrival, &proc[i].priority);
	        	proc[i].remaining = proc[i].burst;
	        	proc[i].completed = 0;
	    	}
	
	    	printf("Enter choice for approach\n");
	    	printf("1. First come first serve\n");
	    	printf("2. Shortest job first\n");
	    	printf("3. Shortest remaining time first\n");
	    	printf("4. Priority\n");
	    	printf("5. Round robin\n");
	    	scanf("%d",&choice);
	
	    	switch (choice)
	    	{
	    	case 1:
	        	fcfsScheduling(proc, n);
	        	break;
	
	    	case 2:
	        	sjfScheduling(proc, n);
	        	break;
	
	    	case 3:
	        	srtfScheduling(proc, n);
	        	break;
	        
	    	case 4:
	        	priorityScheduling(proc, n);
	        	break;
	
	    	case 5:
	        	printf("Enter the time quantum for Round Robin scheduling: ");
	        	scanf("%d", &quantum);
	        	roundRobinScheduling(proc, n, quantum);        
	        	break;
	    
	    	default:
	        	printf("Invalid choice");
	        	break;
	    	}
	    	return 0;
	}
	
	PRODUCE AND CONSUMER 
	
	#include <stdio.h>
	#include <stdlib.h>
	#include <pthread.h>
	#include <semaphore.h>
	#include <unistd.h>
	
	#define BUFFER_SIZE 5  // Size of the shared buffer
	
	int buffer[BUFFER_SIZE];  // Shared buffer
	int in = 0;               // Index for producer
	int out = 0;              // Index for consumer
	sem_t empty;
	sem_t full;
	sem_t mutex;
	
	void* producer(void* arg) {
	    int item;
	
	    while (1) {
	        item = rand() % 100;
	
	        sem_wait(&empty);
	
	        sem_wait(&mutex);
	
	        buffer[in] = item;
	        printf("Producer produced: %d\n", item);
	        in = (in + 1) % BUFFER_SIZE;
	
	        sem_post(&mutex);
	        sem_post(&full);
	        sleep(rand() % 3);
	    }
	}
	
	void* consumer(void* arg) {
	    int item;
	
	    while (1) {
	        sem_wait(&full);
	        sem_wait(&mutex);
	
	        item = buffer[out];
	        printf("Consumer consumed: %d\n", item);
	        out = (out + 1) % BUFFER_SIZE;
	
	        sem_post(&mutex);
	        sem_post(&empty);
	        sleep(rand() % 3);
	    }
	}
	
	int main() {
	    pthread_t prod_thread, cons_thread;
	
	    sem_init(&empty, 0, BUFFER_SIZE);
	    sem_init(&full, 0, 0);
	    sem_init(&mutex, 0, 1);
	
	    pthread_create(&prod_thread, NULL, producer, NULL);
	    pthread_create(&cons_thread, NULL, consumer, NULL);
	
	    pthread_join(prod_thread, NULL);
	    pthread_join(cons_thread, NULL);
	
	    sem_destroy(&empty);
	    sem_destroy(&full);
	    sem_destroy(&mutex);
	
	    return 0;
	}
	
	// gcc -pthread -o producer_consumer_sem producer_consumer_sem.c
	// ./producer_consumer_sem
	
	READER AND WRITTER
	
	#include <stdio.h>
	#include <stdlib.h>
	#include <pthread.h>
	#include <semaphore.h>
	#include <unistd.h>
	
	sem_t rw_mutex;  // Semaphore for writer mutual exclusion
	sem_t mutex;     // Semaphore to protect the reader count
	int reader_count = 0;  // Number of active readers
	int shared_data = 0;   // Shared data that readers and writers access
	
	void* reader(void* arg) {
	    int reader_id = *(int*)arg;
	
	    while (1) {
	        sem_wait(&mutex);
	        reader_count++;
	        if (reader_count == 1) {
	            sem_wait(&rw_mutex);
	        sem_post(&mutex);
	
	        printf("Reader %d is reading the shared data: %d\n", reader_id, shared_data);
	        sleep(rand() % 3);
	
	        sem_wait(&mutex);
	        reader_count--;
	        if (reader_count == 0) {
	            sem_post(&rw_mutex);
	        }
	        sem_post(&mutex);
	
	        sleep(rand() % 5);
	    }
	
	    pthread_exit(NULL);
	}
	
	void* writer(void* arg) {
	    int writer_id = *(int*)arg;
	
	    while (1) {
	        sem_wait(&rw_mutex);
	
	        shared_data = rand() % 100;
	        printf("Writer %d wrote the shared data: %d\n", writer_id, shared_data);
	        sleep(rand() % 3);
	
	        sem_post(&rw_mutex);
	        sleep(rand() % 5);
	    }
	    pthread_exit(NULL);
	}
	
	int main() {
	    int num_readers = 5;
	    int num_writers = 2;
	
	    pthread_t readers[num_readers], writers[num_writers];
	    int reader_ids[num_readers], writer_ids[num_writers];
	
	    sem_init(&rw_mutex, 0, 1);
	    sem_init(&mutex, 0, 1);
	
	    for (int i = 0; i < num_readers; i++) {
	        reader_ids[i] = i + 1;
	        pthread_create(&readers[i], NULL, reader, &reader_ids[i]);
	    }
	
	    for (int i = 0; i < num_writers; i++) {
	        writer_ids[i] = i + 1;
	        pthread_create(&writers[i], NULL, writer, &writer_ids[i]);
	    }
	
	    for (int i = 0; i < num_readers; i++) {
	        pthread_join(readers[i], NULL);
	    }
	
	    for (int i = 0; i < num_writers; i++) {
	        pthread_join(writers[i], NULL);
	    }
	
	    sem_destroy(&rw_mutex);
	    sem_destroy(&mutex);
	    return 0;
	}
	
	DINING PHILOSOPHER
	
	#include <stdio.h>
	#include <stdlib.h>
	#include <pthread.h>
	#include <semaphore.h>
	#include <unistd.h>
	
	#define N 5  // Number of philosophers
	#define THINKING 0
	#define HUNGRY 1
	#define EATING 2
	#define LEFT (ph_num + 4) % N
	#define RIGHT (ph_num + 1) % N
	
	sem_t mutex;
	sem_t S[N];
	int state[N];
	int phil_num[N] = {0, 1, 2, 3, 4};
	
	void test(int ph_num) {
	    if (state[ph_num] == HUNGRY && state[LEFT] != EATING && state[RIGHT] != EATING) {
	        state[ph_num] = EATING;
	        printf("Philosopher %d takes fork %d and %d\n", ph_num + 1, LEFT + 1, ph_num + 1);
	        printf("Philosopher %d is eating\n", ph_num + 1);
	        sem_post(&S[ph_num]);
	    }
	}
	
	void take_fork(int ph_num) {
	    sem_wait(&mutex);
	
	    state[ph_num] = HUNGRY;
	    printf("Philosopher %d is hungry\n", ph_num + 1);
	
	    test(ph_num);
	    sem_post(&mutex);
	    sem_wait(&S[ph_num]);
	    sleep(1);
	}
	
	void put_fork(int ph_num) {
	    sem_wait(&mutex);
	
	    state[ph_num] = THINKING;
	    printf("Philosopher %d putting fork %d and %d down\n", ph_num + 1, LEFT + 1, ph_num + 1);
	    printf("Philosopher %d is thinking\n", ph_num + 1);
	
	    test(LEFT);
	    test(RIGHT);
	
	    sem_post(&mutex);
	}
	
	void* philosopher(void* num) {
	    while (1) {
	        int* i = num;
	        sleep(1);
	        take_fork(*i);
	        sleep(1);
	        put_fork(*i);
	    }
	}
	
	int main() {
	    int i;
	    pthread_t thread_id[N];
	
	    sem_init(&mutex, 0, 1);
	    for (i = 0; i < N; i++) {
	        sem_init(&S[i], 0, 0);
	    }
	
	    for (i = 0; i < N; i++) {
	        pthread_create(&thread_id[i], NULL, philosopher, &phil_num[i]);
	        printf("Philosopher %d is thinking\n", i + 1);
	    }
	
	    for (i = 0; i < N; i++) {
	        pthread_join(thread_id[i], NULL);
	    }
	
	    sem_destroy(&mutex);
	    for (i = 0; i < N; i++) {
	        sem_destroy(&S[i]);
	    }
	    return 0;
	}
	
	BANKER
	
	#include <stdio.h>
	#include <stdbool.h>
	
	#define MAX_PROCESSES 10
	#define MAX_RESOURCES 10
	
	int n, m;  // n = number of processes, m = number of resources
	int allocation[MAX_PROCESSES][MAX_RESOURCES], maximum[MAX_PROCESSES][MAX_RESOURCES];
	int available[MAX_RESOURCES], need[MAX_PROCESSES][MAX_RESOURCES];
	
	bool is_safe_state() {
	    int work[MAX_RESOURCES], finish[MAX_PROCESSES] = {0};
	    int safe_sequence[MAX_PROCESSES];
	    int count = 0;
	
	    for (int i = 0; i < m; i++) {
	        work[i] = available[i];
	    }
	
	    while (count < n) {
	        bool found = false;
	        for (int i = 0; i < n; i++) {
	            if (!finish[i]) {
	                bool possible = true;
	                for (int j = 0; j < m; j++) {
	                    if (need[i][j] > work[j]) {
	                        possible = false;
	                        break;
	                    }
	                }
	
	                if (possible) {
	                    for (int k = 0; k < m; k++) {
	                        work[k] += allocation[i][k];
	                    }
	                    safe_sequence[count++] = i;
	                    finish[i] = 1;
	                    found = true;
	                }
	            }
	        }
	
	        if (!found) {
	            printf("The system is not in a safe state.\n");
	            return false;
	        }
	    }
	
	    printf("The system is in a safe state.\n");
	    printf("Safe sequence: ");
	    for (int i = 0; i < n; i++) {
	        printf("%d ", safe_sequence[i]);
	    }
	    printf("\n");
	
	    return true;
	}
	
	bool request_resources(int process_id, int request[]) {
	    for (int i = 0; i < m; i++) {
	        if (request[i] > need[process_id][i]) {
	            printf("Error: Process %d requested more resources than needed.\n", process_id);
	            return false;
	        }
	    }
	
	    for (int i = 0; i < m; i++) {
	        if (request[i] > available[i]) {
	            printf("Error: Process %d requested more resources than available.\n", process_id);
	            return false;
	        }
	    }
	
	    for (int i = 0; i < m; i++) {
	        available[i] -= request[i];
	        allocation[process_id][i] += request[i];
	        need[process_id][i] -= request[i];
	    }
	
	    if (!is_safe_state()) {
	        for (int i = 0; i < m; i++) {
	            available[i] += request[i];
	            allocation[process_id][i] -= request[i];
	            need[process_id][i] += request[i];
	        }
	        printf("Request cannot be granted as it leads to an unsafe state.\n");
	        return false;
	    }
	
	    printf("Request granted to process %d.\n", process_id);
	    return true;
	}
	
	int main() {
	    printf("Enter the number of processes: ");
	    scanf("%d", &n);
	    printf("Enter the number of resources: ");
	    scanf("%d", &m);
	
	    printf("Enter the allocation matrix:\n");
	    for (int i = 0; i < n; i++) {
	        for (int j = 0; j < m; j++) {
	            scanf("%d", &allocation[i][j]);
	        }
	    }
	
	    printf("Enter the maximum matrix:\n");
	    for (int i = 0; i < n; i++) {
	        for (int j = 0; j < m; j++) {
	            scanf("%d", &maximum[i][j]);
	        }
	    }
	
	    printf("Enter the available resources:\n");
	    for (int i = 0; i < m; i++) {
	        scanf("%d", &available[i]);
	    }
	
	    for (int i = 0; i < n; i++) {
	        for (int j = 0; j < m; j++) {
	            need[i][j] = maximum[i][j] - allocation[i][j];
	        }
	    }
	
	    is_safe_state();
	
	    int process_id;
	    printf("Enter the process number to request resources for: ");
	    scanf("%d", &process_id);
	
	    int request[MAX_RESOURCES];
	    printf("Enter the request vector for process %d: ", process_id);
	    for (int i = 0; i < m; i++) {
	        scanf("%d", &request[i]);
	    }
	    request_resources(process_id, request);
	    return 0;
	}
	
	DA 3
	
	#include<sys/types.h>
	#include<stdio.h>
	#include<unistd.h>
	#include<string.h>
	
	int main(){
	char msg1[100], msg2[100];
	pid_t pid;
	int pd[2];
	strcpy(msg1, "Hello Child");
	
	if(pipe(pd)<0){
	perror("pipe");
	return 1;
	}
	
	pid = fork();
	if(pid < 0){
	perror("fork");
	return 1;
	}
	
	if(pid>0){
	close(pd[0]);
	printf("parent writes : %s\n", msg1);
	write(pd[1], msg1, strlen(msg1)+1);
	}
	
	else{
	close(pd[1]);
	read(pd[0], msg2, sizeof(msg2));
	printf("\nChild reads : %s\n", msg2);
	}
	
	return 0;
	}
	
	//----------------------------------------
	
	#include<sys/types.h>
	#include<stdio.h>
	#include<unistd.h>
	#include<string.h>
	
	int main(){
	int pd[2];
	pid_t pid;
	if(pipe(pd) < 0){
	perror("pipe");
	return 1;
	}
	
	pid = fork();
	if(pid < 0){
	perror("fork");
	return 1;
	}
	
	if(pid == 0){
	close(1);
	dup(pd[1]);
	close(pd[0]);
	close(pd[1]);
	execlp("/usr/bin/who", "who", NULL);
	}
	
	else{
	close(0);
	dup(pd[0]);
	close(pd[0]);
	close(pd[1]);
	execlp("/bin/grep", "who", "24mcs0001", NULL);
	}
	}
	
	//------------------------------------------------
	
	#include<stdio.h>
	#inlclude<stdlib.h>
	#include<fcntl.h>
	#include<unistd.h>
	
	#define BUFFER_SIZE 1024
	
	void copyFile(const char *source, const char *destination){
	
	int src_fd, dest_fd;
	ssize_t nread;
	char buffer[BUFFER_SIZE];
	src_fd = open(source, O_RDONLY);
	if(src_fd == -1){
	perror("Error opening source file");
	exit(EXIT_FAILURE);
	}
	
	dest_fd = open(destination, O_WRONLY | O_CREAT | O_TRUNC, 0644);
	if(dest_fd == -1){
	perror("Error opening destination file");
	close(src_fd);
	exit(EXIT_FAILTURE);
	}
	
	while((nread = read(src_fd, buffer, BUFFER_SIZE)) > 0){
	if (write(dest_fd, bffer, nread) != nread){
	perror("error wrinting to destination file");
	close(src_fd);
	close(dest_fd);
	exit(EXIT_FAILURE);
	}
	}
	
	if(nread == -1){
	perror("Error reading from source file");
	}
	
	close(src_fd);
	close(dest_fd);
	
	}
	
	int main(int argc, char *argv[]){
	
	if(argc != 3){
	fprint(Stderr, "Usage : %s <source file> <destination file>\n", argv[0]);
	exit(EXIT_FAILURE);
	}
	
	copyFile(argv[1], argv[2]);
	printf("File '%s' copied to '%s'. \n", argv[1], argv[2]);
	return 0;
	
	}
	
	// ./<output file name> file1.txt file2.txt
