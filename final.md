- 1번
![image](https://user-images.githubusercontent.com/76835313/144991943-4515822c-44b1-4311-a744-675cfda5f053.png)
![image](https://user-images.githubusercontent.com/76835313/144976374-2054f089-ca46-4e15-bc6a-0335033aee2f.png)

- 2번
![image](https://user-images.githubusercontent.com/76835313/144991979-47b3263e-014f-47d2-9a54-a9d417a7aa44.png)
![image](https://user-images.githubusercontent.com/76835313/144781199-72180a78-6256-4087-bcaa-078d2e658fb1.png)
![image](https://user-images.githubusercontent.com/76835313/144781209-9183c427-5b1b-40b8-8293-381aa5d000f7.png)
- 라운드 로빈 수정
![image](https://user-images.githubusercontent.com/76835313/144959043-786d65c8-7fe1-45d7-85ed-f54c01ed47f9.png)

- Feedbock 알고리즘으로 계산 시
![image](https://user-images.githubusercontent.com/76835313/144955300-8966c569-588f-47a0-835d-b57f1878c5a1.png)

- 3번
![image](https://user-images.githubusercontent.com/76835313/144992016-86020e24-a9ea-4447-ab43-5af1179859d2.png)
![image](https://user-images.githubusercontent.com/76835313/144749464-d222f863-356e-4b8a-b4b5-79ed28c5b5c2.png)
![image](https://user-images.githubusercontent.com/76835313/144844740-3ff2b4a3-c6ce-4404-bb57-520ca2c4f96a.png)

- 4번
![image](https://user-images.githubusercontent.com/76835313/144992053-1ec3d256-0145-4b13-9027-006bfd0674aa.png)
![image](https://user-images.githubusercontent.com/76835313/144788968-1318b928-388f-4284-9b7e-1e0ec2ad149f.png)
![image](https://user-images.githubusercontent.com/76835313/144788974-ae359bdd-7b76-47e4-b0cc-9c62b771d3bb.png)
- 5번
![image](https://user-images.githubusercontent.com/76835313/144992091-2b835852-ba3c-42c7-bcfe-9d7d04f67254.png)
![image](https://user-images.githubusercontent.com/76835313/144795512-d296b689-9ac7-4ce7-88eb-c150140ed8d3.png)
![image](https://user-images.githubusercontent.com/76835313/144795517-efa3234a-cf9b-470b-943d-7364325ff4ae.png)
- 6번
![image](https://user-images.githubusercontent.com/76835313/144992115-96740fc6-d8cb-4b47-93c0-aece40dffe76.png)
![image](https://user-images.githubusercontent.com/76835313/144798062-10150b41-c65f-4138-857c-f49d647b8df0.png)
- 7번
![image](https://user-images.githubusercontent.com/76835313/144992137-79da79df-a93a-4a2c-ac4d-5743e75e89bf.png)


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

1. 어떻게 동작하는지
- deadlock_sleep.c 함수에는 do_work_one함수와 do_work_two 함수가 있다. 
- 프로그램이 가만히 있게된다.
2. 출력이 왜 그렇게 나오는지 이유
- 뮤텍스 때문에 dead lock이 걸리는 상황을 보여준다.
- 20초 sleep을 주면서 반드시 dead lock이 걸리는 상황
3. 소크도으에서 설명된 함수들의 의미
- 첫번째 함수인 do_work_one 함수에서 first와 second를 lock을 걸고 20초뒤 다시 lcok을 걸려고 하니 do_work_two함수에서 역시 second_mutex와 first_mutex가 lock이 걸려있다.
- 그래서 모두 아무것도 하지 못하게 된다. 

                Deadlock_nosleep.c
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
                        pthread_create(&tid1  , &attr, do_work_one  , NULL);
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
                        pthread_mutex_lock(&second_mutex  );
                        pthread_mutex_lock(&first_mutex);
                        /**
                         * Do some work in critical section
                         *
                         * We can ensure deadlock by having this thread sleep before releasing the lock. sleep(20);
                         */

                        pthread_mutex_unlock(&first_mutex);
                        pthread_mutex_unlock(&second_mutex);

                        pthread_exit(0);
                }
1. 어떻게 동작하는지
- deadlock이 걸리지 않는 거처럼 보이며 동작한다.
- 그리고 모든 함수가 종료되고 프린트 함수가 동작하며 Parent Done을 출력된다.
2. 출력이 왜 그렇게 나오는지 이유
- deadlock이 걸릴 가능성이있다.
- 첫번째 함수에서 first mutex와 second mutex에 lock을 걸었는데 두번째 함수에서 역시 second_mutex와 first_mutex에 lock이 걸려있기 때문이다.
3. 소스코드에서 설명된 함수들의 의미
- do_work_one에서 pthread_mutex_lock(&first_mutex)는 first mutex를 가지고 다른놈이 lock을 걸지 않았다면 lock을 걸 수 있다. 그 뜻은 내가 차지하는 순간 다른 쓰레드가 차지하지 못하게 한다.
- 그 이후 critical section을 넣어서 프린터와 같은 I/O접근하는 함수를 위치시킨다.
- 다 쓰고나면 pthread_mutex_unlock함수를 사용하여 다시 unlock을 해준다. 
- 8번
![image](https://user-images.githubusercontent.com/76835313/144992155-f6ea429e-5dc5-4de7-8a5a-12931e33984d.png)
