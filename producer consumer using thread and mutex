#include <iostream>
#include <pthread.h>
#include <unistd.h>
#include <vector>

using namespace std;

//sampleinput
//5
//5
//3

vector<int> buffer;
pthread_mutex_t mtx;
pthread_cond_t cv_producer, cv_consumer;

void print_buffer() {
    cout << "Buffer state: [";
    for (size_t i = 0; i < buffer.size(); ++i) {
        cout << buffer[i];
        if (i != buffer.size() - 1) {
            cout << ", ";
        }
    }
    cout << "]" << endl;
}

void* producer(void* arg) {
    int* params = (int*)arg;
    int num_operations = params[0];
    int buffer_size = params[1];

    for (int i = 0; i < num_operations; ++i) {
        pthread_mutex_lock(&mtx);

        // Wait while buffer is full
        cout << "Producer waiting to produce item " << i + 1 << endl;
        while (buffer.size() >= buffer_size) {
            pthread_cond_wait(&cv_producer, &mtx);
        }
        cout << "Producer entered critical section to produce item " << i + 1 << endl;

        // Produce item
        int item = i + 1;
        buffer.push_back(item);
        cout << "Producer produced item " << item << " at position " << buffer.size() - 1 << endl;

        // Print current buffer state
        print_buffer();

        // Notify consumer that there are items to consume
        pthread_cond_signal(&cv_consumer);

        cout << "Producer exited critical section after producing item " << item << endl;

        pthread_mutex_unlock(&mtx);

        // Simulate work delay
        usleep(100000);  // Use microseconds for delay (simulate work)
    }

    return nullptr;
}

void* consumer(void* arg) {
    int* num_operations = (int*)arg;  // Cast arg to int* to retrieve the number of operations
    int operations = *num_operations;

    for (int i = 0; i < operations; ++i) {
        pthread_mutex_lock(&mtx);

        // Wait while buffer is empty
        cout << "Consumer waiting to consume" << endl;
        while (buffer.empty()) {
            pthread_cond_wait(&cv_consumer, &mtx);
        }
        cout << "Consumer entered critical section to consume" << endl;

        // Consume item
        int item = buffer.front();
        buffer.erase(buffer.begin());
        cout << "Consumer consumed item " << item << endl;

        // Print current buffer state
        print_buffer();

        // Notify producer that there is space in the buffer
        pthread_cond_signal(&cv_producer);

        cout << "Consumer exited critical section after consuming item " << item << endl;

        pthread_mutex_unlock(&mtx);

        // Simulate work delay
        usleep(200000);  // Use microseconds for delay (simulate work)
    }

    return nullptr;
}

int main() {
    int num_producer_ops, num_consumer_ops, buffer_size;

    // Taking inputs for number of operations and buffer size
    cout << "Enter number of producer operations: ";
    cin >> num_producer_ops;
    cout << "Enter number of consumer operations: ";
    cin >> num_consumer_ops;
    cout << "Enter buffer size: ";
    cin >> buffer_size;

    // Initialize mutex and condition variables
    pthread_mutex_init(&mtx, nullptr);
    pthread_cond_init(&cv_producer, nullptr);
    pthread_cond_init(&cv_consumer, nullptr);

    // Prepare producer and consumer parameters
    int producer_params[2] = {num_producer_ops, buffer_size};

    // Create producer and consumer threads
    pthread_t producer_thread, consumer_thread;
    pthread_create(&producer_thread, nullptr, producer, (void*)producer_params);
    pthread_create(&consumer_thread, nullptr, consumer, (void*)&num_consumer_ops);

    // Wait for threads to finish
    pthread_join(producer_thread, nullptr);
    pthread_join(consumer_thread, nullptr);

    // Destroy mutex and condition variables
    pthread_mutex_destroy(&mtx);
    pthread_cond_destroy(&cv_producer);
    pthread_cond_destroy(&cv_consumer);

    return 0;
}
