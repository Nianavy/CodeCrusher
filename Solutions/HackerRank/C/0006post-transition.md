https://www.hackerrank.com/challenges/post-transition

```txt
Manage town post offices' packages:
add, remove, query, send between towns,
and find the town with most packages.
```

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define MAX_STRING_LENGTH 6

struct package
{
	char* id;
	int weight;
};

typedef struct package package;

struct post_office
{
	int min_weight;
	int max_weight;
	package* packages;
	int packages_count;
};

typedef struct post_office post_office;

struct town
{
	char* name;
	post_office* offices;
	int offices_count;
};

typedef struct town town;



void print_all_packages(town t) {
    printf("%s:\n", t.name);
    for (int i = 0; i < t.offices_count; ++i) {
        printf("\t%d:\n", i);
        post_office *office = &t.offices[i];
        for (int j = 0; j < office->packages_count; ++j)
            printf("\t\t%s\n", office->packages[j].id, office->packages[j].weight);
    }
}

void send_all_acceptable_packages(town* source, int source_office_index, town* target, int target_office_index) {	
    post_office *src_office = &source->offices[source_office_index];
    post_office *tgt_office = &target->offices[target_office_index];
    
    int src_count = src_office->packages_count;
    int valid_count = 0;
    
    for (int i = 0; i < src_count; ++i) {
        int w = src_office->packages[i].weight;
        if (w >= tgt_office->min_weight && w <= tgt_office->max_weight) valid_count++;
    }
    
    if (valid_count == 0) return;
    
    int old_count = tgt_office->packages_count;
    int new_count = old_count + valid_count;
    tgt_office->packages = (package*)realloc(tgt_office->packages, new_count * sizeof(package));
    if (tgt_office->packages == NULL) exit(EXIT_FAILURE);
    tgt_office->packages_count = new_count;
    
    for (int i = 0; i < src_count; i++) {
        int w = src_office->packages[i].weight;
        if (w >= tgt_office->min_weight && w <= tgt_office->max_weight) {
            package* dst_pkg = &tgt_office->packages[old_count++];
            dst_pkg->id = (char*)malloc(MAX_STRING_LENGTH * sizeof(char));
            strcpy(dst_pkg->id, src_office->packages[i].id);
            dst_pkg->weight = src_office->packages[i].weight;
        }
    }
    
    int j = 0;
    for (int i = 0; i < src_count; ++i) {
        int w = src_office->packages[i].weight;
        if (w < tgt_office->min_weight || w > tgt_office->max_weight) {
            if (j != i) src_office->packages[j] = src_office->packages[i];
            ++j;
        }
        else free(src_office->packages[i].id);
    }
    src_office->packages_count = j;
    
    if (j == 0) {
        free(src_office->packages);
        src_office->packages = NULL;
    }
    else src_office->packages = (package*)realloc(src_office->packages, j * sizeof(package));
}

town town_with_most_packages(town* towns, int towns_count) {
    int max_packages = -1;
    town *result = &towns[0];
    
    for (int i = 0; i < towns_count; ++i) {
        int total = 0;
        for (int j = 0; j < towns[i].offices_count; ++j) total += towns[i].offices[j].packages_count;
        if (total > max_packages) {
            max_packages = total;
            result = &towns[i];
        }
    }
    return *result;
}

town* find_town(town* towns, int towns_count, char* name) {
    for (int i = 0; i < towns_count; ++i) 
        if (strcmp(towns[i].name, name) == 0) return &towns[i];
    return NULL;
}

int main()
{
	int towns_count;
	scanf("%d", &towns_count);
	town* towns = malloc(sizeof(town)*towns_count);
	for (int i = 0; i < towns_count; i++) {
		towns[i].name = malloc(sizeof(char) * MAX_STRING_LENGTH);
		scanf("%s", towns[i].name);
		scanf("%d", &towns[i].offices_count);
		towns[i].offices = malloc(sizeof(post_office)*towns[i].offices_count);
		for (int j = 0; j < towns[i].offices_count; j++) {
			scanf("%d%d%d", &towns[i].offices[j].packages_count, &towns[i].offices[j].min_weight, &towns[i].offices[j].max_weight);
			towns[i].offices[j].packages = malloc(sizeof(package)*towns[i].offices[j].packages_count);
			for (int k = 0; k < towns[i].offices[j].packages_count; k++) {
				towns[i].offices[j].packages[k].id = malloc(sizeof(char) * MAX_STRING_LENGTH);
				scanf("%s", towns[i].offices[j].packages[k].id);
				scanf("%d", &towns[i].offices[j].packages[k].weight);
			}
		}
	}
	int queries;
	scanf("%d", &queries);
	char town_name[MAX_STRING_LENGTH];
	while (queries--) {
		int type;
		scanf("%d", &type);
		switch (type) {
		case 1:
			scanf("%s", town_name);
			town* t = find_town(towns, towns_count, town_name);
			print_all_packages(*t);
			break;
		case 2:
			scanf("%s", town_name);
			town* source = find_town(towns, towns_count, town_name);
			int source_index;
			scanf("%d", &source_index);
			scanf("%s", town_name);
			town* target = find_town(towns, towns_count, town_name);
			int target_index;
			scanf("%d", &target_index);
			send_all_acceptable_packages(source, source_index, target, target_index);
			break;
		case 3:
			printf("Town with the most number of packages is %s\n", town_with_most_packages(towns, towns_count).name);
			break;
		}
	}
	return 0;
}
```
