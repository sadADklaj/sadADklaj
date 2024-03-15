#include <stdio.h>  
#include <stdlib.h>  
#include <string.h>  
#define LEN sizeof(struct stu)

typedef struct stu {
	int number;     // 序号  
    long num;       // 学号  
    char name[50];  // 姓名  
    float score;    // 分数  
    struct stu* next;  
} Student;  

Student* create_student_list(const char* filename) {  
    FILE* file = fopen(filename, "r");  
    if (file == NULL) {  
        perror("打开文件失败");  
        return NULL;  
    }  
  
    Student* head = NULL;  //头 
    Student* tail = NULL;  //尾 
    int count = 0;  //计数变量 
  
    char line[256];  
    while (fgets(line, sizeof(line), file)) {  //遍历每行 
        // 去除行尾的换行符  
        line[strcspn(line, "\n")] = 0;  
  
        // 解析学生信息  
        Student* new_student = (Student*)malloc(LEN);  //分配动态内存 
        if (new_student == NULL) {  
            perror("内存分配失败");  
            fclose(file);  
            return NULL;  
        }  
  
        if (sscanf(line, "%d %d %s %f",&new_student->number, &new_student->num, new_student->name, &new_student->score) != 4) {  
            fprintf(stderr, "录取错误，格式有误\n");  
            free(new_student);  
            continue;  
        }  
  
        // 将新学生添加到链表末尾  
        new_student->next = NULL;  
        if (tail == NULL) {  // 检查链表是否为空（即没有学生）
            head = new_student;  // 如果是空的，新学生就是链表的第一个元素，所以更新头指针
            tail = new_student;   // 同时，新学生也是链表的最后一个元素，所以更新尾指针 
        } else {  // 如果链表不为空，已经有至少一个学生了  
            tail->next = new_student;  // 将当前尾学生的下一个指针指向新学生  
            tail = new_student;  // 更新尾指针，因为新学生现在是链表的最后一个元素  
        }  
  
        count++;  
    }  
    fclose(file);  
    printf("共计录入%d个学生\n", count);  
    return head;  
}

// 打印学生信息的函数  
void print_student(const Student* student) {  
    if (student == NULL) {  
        printf("Student is NULL.\n");  
        return;  
    }  
  
    // 打印当前学生的信息  
    printf("Student Number: %ld\n", student->num);  
    printf("Name: %s\n", student->name);  
    printf("Score: %.2f\n", student->score);  
  
    // 如果链表中有更多的学生，递归打印他们的信息  
    if (student->next != NULL) {  
        print_student(student->next);  
    }    
}  
  
// 打印平均分的函数  
void print_average_score(float sum, int count) {  
        if (count == 0) {  
        printf("No students to calculate average.\n");  
        return;  
    }  
  
    // 计算平均分  
    float average = sum / count;  
  
    // 打印平均分，保留两位小数  
    printf("Average score: %.2f\n", average);  
}  
  
// 打印最高分和最低分的函数  
void print_highest_and_lowest_scores(const Student* head) {  
        if (head == NULL) {  
        printf("List is empty.\n");  
        return;  
    }  
  
    float highest_score = head->score;  
    float lowest_score = head->score;  
  
    for (const Student* student = head; student != NULL; student = student->next) {  
        if (student->score > highest_score) {  
            highest_score = student->score;  
        }  
        if (student->score < lowest_score) {  
            lowest_score = student->score;  
        }  
    }  
  
    // 打印最高分和最低分  
    printf("Highest score: %.2f\n", highest_score);  
    printf("Lowest score: %.2f\n", lowest_score); 
}  
  
// 主函数  
int main() {  
    const char* filename = "成绩.txt";  
    Student* head = create_student_list(filename);  
    int count = 0;  
    float sum = 0.0;  
    Student* current = head;  
    while (current != NULL) {  
        count++;  
        sum += current->score;  
        current = current->next;  
    }   
    print_average_score(sum, count);  
    print_highest_and_lowest_scores(head);  
  
    // 释放链表内存  
    current = head;  
    while (current != NULL) {  
        Student* temp = current;  
        current = current->next; 
        free(temp);  
    }  
    return 0;  
}
