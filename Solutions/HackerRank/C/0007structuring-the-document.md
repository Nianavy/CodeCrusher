https://www.hackerrank.com/challenges/structuring-the-document

```txt
Parse text layer by layer, manually split words and sentences,
ensuring memory independence and structural correctness.
```

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <assert.h>
#define MAX_CHARACTERS 1005
#define MAX_PARAGRAPHS 5

struct word {
    char* data;
};

struct sentence {
    struct word* data;
    int word_count;//denotes number of words in a sentence
};

struct paragraph {
    struct sentence* data  ;
    int sentence_count;//denotes number of sentences in a paragraph
};

struct document {
    struct paragraph* data;
    int paragraph_count;//denotes number of paragraphs in a document
};

struct document get_document(char* text) {
    struct document doc;
    doc.paragraph_count = 0;
    int len = strlen(text);
    int i = 0;

    while (i < len) {
        if (text[i] == '\n') doc.paragraph_count++;
        i++;
    }
    doc.paragraph_count++;
    doc.data = calloc(doc.paragraph_count, sizeof(struct paragraph));
    assert(doc.data != NULL);

    char* start = text;
    char* end;
    i = 0;

    while (i < doc.paragraph_count) {
        end = strchr(start, '\n');
        if (end == NULL) end = text + len;

        int para_len = end - start;
        char* para_str = malloc(para_len + 1);
        memcpy(para_str, start, para_len);
        para_str[para_len] = '\0';

        int s_count = 0;
        char* p = para_str;
        while (*p) {
            if (*p == '.') s_count++;
            p++;
        }

        doc.data[i].sentence_count = s_count;
        doc.data[i].data = calloc(s_count, sizeof(struct sentence));
        assert(doc.data[i].data != NULL);

        char* s_start = para_str;
        char* s_end;
        int s_idx = 0;

        while (s_idx < s_count) {
            s_end = strchr(s_start, '.');
            if (s_end == NULL) s_end = para_str + para_len;

            int sent_len = s_end - s_start;
            char* sent_str = malloc(sent_len + 1);
            memcpy(sent_str, s_start, sent_len);
            sent_str[sent_len] = '\0';

            int w_count = 0;
            char* w = sent_str;
            int in_word = 0;
            while (*w) {
                if (*w != ' ' && *w != '\t' && *w != '\n' && *w != '\r') {
                    if (!in_word) {
                        w_count++;
                        in_word = 1;
                    }
                } else {
                    in_word = 0;
                }
                w++;
            }

            doc.data[i].data[s_idx].word_count = w_count;
            doc.data[i].data[s_idx].data = calloc(w_count, sizeof(struct word));
            assert(doc.data[i].data[s_idx].data != NULL);

            char* w_start = sent_str;
            char* w_end;
            int w_idx = 0;
            while (w_idx < w_count) {
                while (*w_start == ' ' || *w_start == '\t') w_start++;
                w_end = w_start;
                while (*w_end != ' ' && *w_end != '\t' && *w_end != '\0') w_end++;
                int word_len = w_end - w_start;
                doc.data[i].data[s_idx].data[w_idx].data = malloc(word_len + 1);
                memcpy(doc.data[i].data[s_idx].data[w_idx].data, w_start, word_len);
                doc.data[i].data[s_idx].data[w_idx].data[word_len] = '\0';
                w_start = w_end;
                w_idx++;
            }

            free(sent_str);
            s_start = s_end + 1;
            s_idx++;
        }

        free(para_str);
        start = end + 1;
        i++;
    }

    return doc;
}

struct word kth_word_in_mth_sentence_of_nth_paragraph(struct document Doc, int k, int m, int n) {
    return Doc.data[n-1].data[m-1].data[k-1];
}

struct sentence kth_sentence_in_mth_paragraph(struct document Doc, int k, int m) {
    return Doc.data[m-1].data[k-1];
}

struct paragraph kth_paragraph(struct document Doc, int k) {
    return Doc.data[k-1];
}


void print_word(struct word w) {
    printf("%s", w.data);
}

void print_sentence(struct sentence sen) {
    for(int i = 0; i < sen.word_count; i++) {
        print_word(sen.data[i]);
        if (i != sen.word_count - 1) {
            printf(" ");
        }
    }
}

void print_paragraph(struct paragraph para) {
    for(int i = 0; i < para.sentence_count; i++){
        print_sentence(para.data[i]);
        printf(".");
    }
}

void print_document(struct document doc) {
    for(int i = 0; i < doc.paragraph_count; i++) {
        print_paragraph(doc.data[i]);
        if (i != doc.paragraph_count - 1)
            printf("\n");
    }
}

char* get_input_text() {	
    int paragraph_count;
    scanf("%d", &paragraph_count);

    char p[MAX_PARAGRAPHS][MAX_CHARACTERS], doc[MAX_CHARACTERS];
    memset(doc, 0, sizeof(doc));
    getchar();
    for (int i = 0; i < paragraph_count; i++) {
        scanf("%[^\n]%*c", p[i]);
        strcat(doc, p[i]);
        if (i != paragraph_count - 1)
            strcat(doc, "\n");
    }

    char* returnDoc = (char*)malloc((strlen (doc)+1) * (sizeof(char)));
    strcpy(returnDoc, doc);
    return returnDoc;
}

int main() 
{
    char* text = get_input_text();
    struct document Doc = get_document(text);

    int q;
    scanf("%d", &q);

    while (q--) {
        int type;
        scanf("%d", &type);

        if (type == 3){
            int k, m, n;
            scanf("%d %d %d", &k, &m, &n);
            struct word w = kth_word_in_mth_sentence_of_nth_paragraph(Doc, k, m, n);
            print_word(w);
        }

        else if (type == 2) {
            int k, m;
            scanf("%d %d", &k, &m);
            struct sentence sen= kth_sentence_in_mth_paragraph(Doc, k, m);
            print_sentence(sen);
        }

        else{
            int k;
            scanf("%d", &k);
            struct paragraph para = kth_paragraph(Doc, k);
            print_paragraph(para);
        }
        printf("\n");
    }     
}
```
