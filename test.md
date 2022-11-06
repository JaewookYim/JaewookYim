**- 👋 Hi, I’m @JaewookYim
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
JaewookYim/JaewookYim is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
#include <stdio.h>
#include <stdlib.h>

struct list_head {
	struct list_head *next, *prev;
};

void __list_add(struct list_head *new,
		struct list_head *prev,
		struct list_head *next)
{
	next->prev = new;
	new->next = next;
	new->prev = prev;
	prev->next = new;
}

void list_add( struct list_head *new, 
		struct list_head *head )
{
	__list_add(new, head, head->next);
}

void list_add_tail(struct list_head *new, struct list_head *head)
{
    __list_add(new, head->prev, head);
}

#define offsetof(TYPE, MEMBER)	((size_t)&((TYPE *)0)->MEMBER)

#define container_of(ptr, type, member) ({			\
	const typeof( ((type *)0)->member ) *__mptr = (ptr);	\
	(type *)( (char *)__mptr - offsetof(type,member) );})

//-----------------------------------------

struct inode
{
	unsigned long       i_ino;
	struct list_head    i_wb_list;  /* backing dev IO list */
	struct list_head    i_lru;      /* inode LRU list */
	struct list_head    i_sb_list;
};

#if 0
void display(struct list_head *head)
{
	struct list_head *temp;
	struct inode *p;
	system("clear");
	printf("[head]");
	for( temp = head->next; temp != head ; temp=temp->next )
	{
		p = container_of( temp, struct inode, i_lru );
		printf("<->[%lu]", p->i_ino);
	}
	printf("<->[head]\n");
	getchar();
}

int main()
{
	struct list_head head = {&head,&head};
	struct inode inodes[2] = { {1000}, {2000} };
	int i;
	display(&head);
	for(i=0; i<2; i++ )
	{
		list_add_tail( &inodes[i].i_lru , &head );
		display(&head);
	}
	return 0;
}
#endif



struct hlist_head {
	struct hlist_node *first;
};

struct hlist_node {
	struct hlist_node *next, **pprev;
};

void hlist_add_head(struct hlist_node *n, struct hlist_head *h)
{
	struct hlist_node *first = h->first;
	n->next = first;
	if (first)
		first->pprev = &n->next;
	h->first = n;
	n->pprev = &h->first;
}

#define offsetof(TYPE, MEMBER)	((size_t)&((TYPE *)0)->MEMBER)
#define container_of(ptr, type, member) ({                      \
	const typeof( ((type *)0)->member ) *__mptr = (ptr);    \
	(type *)( (char *)__mptr - offsetof(type,member) );})

#define hlist_entry(ptr, type, member) container_of(ptr,type,member)

/**
 * hlist_for_each_entry - iterate over list of given type
 * @tpos:   the type * to use as a loop cursor.
 * @pos:    the &struct hlist_node to use as a loop cursor.
 * @head:   the head for your list.
 * @member: the name of the hlist_node within the struct.
 */
#define hlist_for_each_entry(tpos, pos, head, member)            \
    for (pos = (head)->first;                     \
         pos &&                          \
        ({ tpos = hlist_entry(pos, typeof(*tpos), member); 1;}); \
         pos = pos->next)



#define  HASH_MAX   8

#define GOLDEN_RATIO_PRIME_32 0x9e370001UL

unsigned int hash_32(unsigned int val, unsigned int bits)
{
	unsigned int hash = val * GOLDEN_RATIO_PRIME_32;
	return hash >> (32 - bits);
}  

#define pid_hashfn(pid)  \
	    hash_32( pid, 3 )    

typedef struct
{
	int sno;
	struct hlist_node hash;
} SAWON;

int hash_sno( int sno )
{
	return hash_32( sno, 3 );
}

#if 0
void display( struct hlist_head *heads )
{
	int i;
	struct hlist_node *temp;
	SAWON *s;
	system("clear");
	for( i=0; i<HASH_MAX; i++ )
	{
		printf("[%d]", i );
		for( temp = heads[i].first; temp; temp=temp->next)
		{
			s = container_of( temp, SAWON, hash );
			printf("<->[%d]", s->sno );
		}
		printf("\n");
	}
	getchar();
}
#endif

typedef struct page
{
  int pfn;
  int data;
  struct hlist_node hnode;
  struct list_head list;
}PAGE;

#if 0
int main()
{
	struct hlist_head heads[ HASH_MAX ] = {0,};
	SAWON s[30] = {0,};
	int i;
	int sno;

	display( heads );
	for(i=0; i<30; i++ )
	{
		sno = 1000+i;
		s[i].sno = sno;
		hlist_add_head( &s[i].hash, &heads[hash_sno(sno)]);
		display( heads );
	}
	return 0;
}
#endif

#define NUM_OF_PAGES  (40)

void display(struct list_head *head, struct hlist_head *heads )
{
	int i;
	struct hlist_node *temp;
  struct list_head *temp_lru;
	PAGE *p;
	system("clear");
  printf("[HASH TABLE]\n");
	for( i=0; i<HASH_MAX; i++ )
	{
		printf("[%d]", i );
		for( temp = heads[i].first; temp; temp=temp->next)
		{
			p = container_of( temp, PAGE, hnode );
			printf("<->[%d]", p->pfn );
		}
		printf("\n");
	}
  printf("\n\n");

  printf("[LRU LIST]");
	for( temp_lru = head->next; temp_lru != head ; temp_lru=temp_lru->next )
	{
		p = container_of( temp_lru, PAGE, list );
		printf("<->[%d]", p->pfn);
  }
}

int main()
{
  struct list_head head = {&head,&head};
  struct hlist_head heads[HASH_MAX] = {0,};
  PAGE pages[NUM_OF_PAGES] = {0, };

  for(int i = 0; i < NUM_OF_PAGES; i++)
  {
    pages[i].pfn = i;
    pages[i].data = 1000+i;    
  }

	display( &head, heads );
  while(1)
  {
    getchar();
    int indexOfPages = rand()%40;

    hlist_for_each_entry()
    
    hlist_add_head( &pages[indexOfPages].hnode, &heads[hash_sno(indexOfPages)]);  
    list_add(&pages[indexOfPages].list, &head);
	  display( &head, heads );
  }
}


**#include <stdio.h>
#include <stdlib.h>

struct list_head {
	struct list_head *next, *prev;
};

void __list_add(struct list_head *new,
		struct list_head *prev,
		struct list_head *next)
{
	next->prev = new;
	new->next = next;
	new->prev = prev;
	prev->next = new;
}

void list_add( struct list_head *new, 
		struct list_head *head )
{
	__list_add(new, head, head->next);
}

void list_add_tail(struct list_head *new, struct list_head *head)
{
    __list_add(new, head->prev, head);
}

#define offsetof(TYPE, MEMBER)	((size_t)&((TYPE *)0)->MEMBER)

#define container_of(ptr, type, member) ({			\
	const typeof( ((type *)0)->member ) *__mptr = (ptr);	\
	(type *)( (char *)__mptr - offsetof(type,member) );})

//-----------------------------------------

struct inode
{
	unsigned long       i_ino;
	struct list_head    i_wb_list;  /* backing dev IO list */
	struct list_head    i_lru;      /* inode LRU list */
	struct list_head    i_sb_list;
};

#if 0
void display(struct list_head *head)
{
	struct list_head *temp;
	struct inode *p;
	system("clear");
	printf("[head]");
	for( temp = head->next; temp != head ; temp=temp->next )
	{
		p = container_of( temp, struct inode, i_lru );
		printf("<->[%lu]", p->i_ino);
	}
	printf("<->[head]\n");
	getchar();
}

int main()
{
	struct list_head head = {&head,&head};
	struct inode inodes[2] = { {1000}, {2000} };
	int i;
	display(&head);
	for(i=0; i<2; i++ )
	{
		list_add_tail( &inodes[i].i_lru , &head );
		display(&head);
	}
	return 0;
}
#endif



struct hlist_head {
	struct hlist_node *first;
};

struct hlist_node {
	struct hlist_node *next, **pprev;
};

void hlist_add_head(struct hlist_node *n, struct hlist_head *h)
{
	struct hlist_node *first = h->first;
	n->next = first;
	if (first)
		first->pprev = &n->next;
	h->first = n;
	n->pprev = &h->first;
}

#define offsetof(TYPE, MEMBER)	((size_t)&((TYPE *)0)->MEMBER)
#define container_of(ptr, type, member) ({                      \
	const typeof( ((type *)0)->member ) *__mptr = (ptr);    \
	(type *)( (char *)__mptr - offsetof(type,member) );})

#define hlist_entry(ptr, type, member) container_of(ptr,type,member)

/**
 * hlist_for_each_entry - iterate over list of given type
 * @tpos:   the type * to use as a loop cursor.
 * @pos:    the &struct hlist_node to use as a loop cursor.
 * @head:   the head for your list.
 * @member: the name of the hlist_node within the struct.
 */
#define hlist_for_each_entry(tpos, pos, head, member)            \
    for (pos = (head)->first;                     \
         pos &&                          \
        ({ tpos = hlist_entry(pos, typeof(*tpos), member); 1;}); \
         pos = pos->next)



#define  HASH_MAX   8

#define GOLDEN_RATIO_PRIME_32 0x9e370001UL

unsigned int hash_32(unsigned int val, unsigned int bits)
{
	unsigned int hash = val * GOLDEN_RATIO_PRIME_32;
	return hash >> (32 - bits);
}  

#define pid_hashfn(pid)  \
	    hash_32( pid, 3 )    

typedef struct
{
	int sno;
	struct hlist_node hash;
} SAWON;

int hash_sno( int sno )
{
	return hash_32( sno, 3 );
}

#if 0
void display( struct hlist_head *heads )
{
	int i;
	struct hlist_node *temp;
	SAWON *s;
	system("clear");
	for( i=0; i<HASH_MAX; i++ )
	{
		printf("[%d]", i );
		for( temp = heads[i].first; temp; temp=temp->next)
		{
			s = container_of( temp, SAWON, hash );
			printf("<->[%d]", s->sno );
		}
		printf("\n");
	}
	getchar();
}
#endif

typedef struct page
{
  int pfn;
  int data;
  struct hlist_node hnode;
  struct list_head list;
}PAGE;

#if 0
int main()
{
	struct hlist_head heads[ HASH_MAX ] = {0,};
	SAWON s[30] = {0,};
	int i;
	int sno;

	display( heads );
	for(i=0; i<30; i++ )
	{
		sno = 1000+i;
		s[i].sno = sno;
		hlist_add_head( &s[i].hash, &heads[hash_sno(sno)]);
		display( heads );
	}
	return 0;
}
#endif

#define NUM_OF_PAGES  (40)

void display(struct list_head *head, struct hlist_head *heads )
{
	int i;
	struct hlist_node *temp;
  struct list_head *temp_lru;
	PAGE *p;
	system("clear");
  printf("[HASH TABLE]\n");
	for( i=0; i<HASH_MAX; i++ )
	{
		printf("[%d]", i );
		for( temp = heads[i].first; temp; temp=temp->next)
		{
			p = container_of( temp, PAGE, hnode );
			printf("<->[%d]", p->pfn );
		}
		printf("\n");
	}
  printf("\n\n");

  printf("[LRU LIST]");
	for( temp_lru = head->next; temp_lru != head ; temp_lru=temp_lru->next )
	{
		p = container_of( temp_lru, PAGE, list );
		printf("<->[%d]", p->pfn);
  }
}

int main()
{
  struct list_head head = {&head,&head};
  struct hlist_head heads[HASH_MAX] = {0,};
  PAGE pages[NUM_OF_PAGES] = {0, };

  for(int i = 0; i < NUM_OF_PAGES; i++)
  {
    pages[i].pfn = i;
    pages[i].data = 1000+i;    
  }

	display( &head, heads );
  while(1)
  {
    getchar();
    int indexOfPages = rand()%40;

    hlist_for_each_entry()
    
    hlist_add_head( &pages[indexOfPages].hnode, &heads[hash_sno(indexOfPages)]);  
    list_add(&pages[indexOfPages].list, &head);
	  display( &head, heads );
  }
}


