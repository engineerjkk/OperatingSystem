- 2번
![image](https://user-images.githubusercontent.com/76835313/144781199-72180a78-6256-4087-bcaa-078d2e658fb1.png)
![image](https://user-images.githubusercontent.com/76835313/144781209-9183c427-5b1b-40b8-8293-381aa5d000f7.png)

- 3번
![image](https://user-images.githubusercontent.com/76835313/144749464-d222f863-356e-4b8a-b4b5-79ed28c5b5c2.png)
![image](https://user-images.githubusercontent.com/76835313/144749471-a23ae50c-71c8-43b1-b3ed-f7adf11a91c7.png)
- 4번
![image](https://user-images.githubusercontent.com/76835313/144788968-1318b928-388f-4284-9b7e-1e0ec2ad149f.png)
![image](https://user-images.githubusercontent.com/76835313/144788974-ae359bdd-7b76-47e4-b0cc-9c62b771d3bb.png)
- 5번
![image](https://user-images.githubusercontent.com/76835313/144795512-d296b689-9ac7-4ce7-88eb-c150140ed8d3.png)
![image](https://user-images.githubusercontent.com/76835313/144795517-efa3234a-cf9b-470b-943d-7364325ff4ae.png)
- 6번
![image](https://user-images.githubusercontent.com/76835313/144798062-10150b41-c65f-4138-857c-f49d647b8df0.png)
- 7번

        Deadlock_sleep.c
        -bash - 3.2$ cat deadlock_sleep.c
        /**
         * A pthread program illustrating deadlock.
         *
         * Usage
         *      gcc deadlock.c -lpthread
         *      ./a.out
         *
         * Figure 7.1
         *
         * @author Gagne, Galvin, Silberschatz
         * Operating System Concepts  - Seventh Edition
         * Copyright John Wiley & Sons - 2005.
         */

        #include <pthread.h>
        #include <stdio.h>

          pthread_mutex_t first_mutex;
        pthread_mutex_t second_mutex;

        void *do_work_one(void *param);
        void *do_work_two(void *param);  

        int main(int argc, char *argv[])
        {
          pthread_t tid1, tid2; /* the thread identifiers */
          pthread_attr_t attr; /* set of attributes for the thread */

          /* get the default attributes */
          pthread_attr_init(&attr);

          /* create the mutex locks */
          pthread_mutex_init(&first_mutex, NULL);
          pthread_mutex_init(&second_mutex, NULL);

          /* create the threads */
          pthread_create(&tid1, &attr, do_work_one, NULL);
          pthread_create(&tid2, &attr, do_work_two, NULL);

          /* now wait for the thread to exit */
          pthread_join(tid1, NULL);
          pthread_join(tid2, NULL);
          printf("Parent DONE\n");
          /* destroy the mutex before exiting */
          pthread_mutex_destroy(&first_mutex);
          pthread_mutex_destroy(&second_mutex);
        }

        void *do_work_one(void *param)
        {
          pthread_mutex_lock(&first_mutex);
          sleep(20);
          pthread_mutex_lock(&second_mutex);
          /**
           * Do some work in critical section
           */
          pthread_mutex_unlock(&second_mutex);
          pthread_mutex_unlock(&first_mutex);

          pthread_exit(0);
        }

        void *do_work_two(void *param)
        {
          pthread_mutex_lock(&second_mutex);
          sleep(20);
          pthread_mutex_lock(&first_mutex);
          /**
           * Do some work in critical section
           *
           * We may ensure deadlock by having this thread sleep before releasing the lock.
           */
          sleep(20);
          pthread_mutex_unlock(&first_mutex);
          pthread_mutex_unlock(&second_mutex);

          pthread_exit(0);
        }

