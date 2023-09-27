<script lang="ts">
	import { Presentation, Slide, Markdown, Code } from '@components'
	import Vertical from '@lib/components/vertical.svelte'
</script>

<Presentation>

	<Slide>
		<p class="pb-6 font-mono text-gray-500">
			Let's begin with some data structures to establish some context
		</p>
	</Slide>

	<Slide>
		<p class="text-[22px]">
			<Code lang="c">
			{`
				// I made a Lot type like so:
				typedef struct {
					int type;
					int quantity;
					int life;
					int reserved;
					LotState state;
				} Lot;
			`}
		</Code>
		</p>
		
	</Slide>



	<Vertical>
		<Slide animate>	
			<p class="text-[22px]">
				<Code lang="c">
				{`
					// And a dynamic collection of Lot types like this:
					typedef struct {
						size_t size;
						size_t cap;
						Lot *items;
					} LotCollection;
				`}
			</Code>
			</p>	
			
		</Slide>

		<Slide animate>		
			<p class="text-[22px]">
				<Code lang="c" lines="8-12">
				{`
					// A dynamic collection of Lot types like this:
					typedef struct {
						size_t size;
						size_t cap;
						Lot *items;
					} LotCollection;

					// and its relative init/add/delete/get methods:
					void initCollection(LotCollection **collection_ptr);
					void insertItem(LotCollection *collection, Lot item);
					Lot  getItem(LotCollection *collection, int i);
					void deleteItem(LotCollection *collection, int index);
				`}
			</Code>
			</p>
			
		</Slide>

		<Slide animate>	
			<p class="text-[22px]">
				<Code lang="c" lines="1-2">
				{`
					// also, the initial size of the collection is 8 Lots
					#define INITIAL_SIZE 8

					// A dynamic collection of Lot types like this:
					typedef struct {
						size_t size;
						size_t cap;
						Lot *items;
					} LotCollection;

					// and its relative init/add/delete/get methods:
					void initCollection(LotCollection **collection_ptr);
					void insertItem(LotCollection *collection, Lot item);
					Lot  getItem(LotCollection *collection, int i);
					void deleteItem(LotCollection *collection, int index);
				`}
			</Code>
			</p>	
			
		</Slide>
	</Vertical>

	<Vertical>
		<Slide animate>		
			<p class="text-[22px]">
				<Code lang="c">
				{`
					// the collection is dynamic, it grows (2 * Capacity) 
					void insertItem(LotCollection *collection, Lot item) {
    					if (collection->size == collection->cap) {
        					Lot *temp = collection->items;
        					collection->cap <<= 1;
        					collection->items = realloc(collection->items, 
														collection->cap * sizeof(Lot));
        					if (!collection->items) {
            					//print out-of-memory
        					}
    					}
    					collection->items[collection->size++] = item;
					}
				`}
			</Code>
			</p>
			
		</Slide>

		<Slide animate>	
			<p class="text-[22px]">
				<Code lang="c" lines="5-7">
				{`
					// the collection is dynamic, it grows (2 * Capacity) 
					void insertItem(LotCollection *collection, Lot item) {
    					if (collection->size == collection->cap) {
        					Lot *temp = collection->items;
        					collection->cap <<= 1; // if you reach the max cap, you double it
        					collection->items = realloc(collection->items, 
														collection->cap * sizeof(Lot));
        					if (!collection->items) {
            					//print out-of-memory
        					}
    					}
    					collection->items[collection->size++] = item;
					}
				`}
			</Code>
			</p>	
			
		</Slide>
	</Vertical>

	<Vertical>
		<p class="text-gray-500">The interesting part begins now</p>
	</Vertical>

	<Vertical>
		<Slide animate>	
			<p class="text-[22px]">
				<Code lang="c">
				{`
//this is how you delete a Lot item from the collection
void deleteItem(LotCollection *collection, int index) {
    int i;

    if (index >= collection->size) {
        printf("Index Out of Bounds\\n");
        return;
    }

    for (i = index; i < collection->size; i++) {
        collection->items[i] = collection->items[i + 1];
    }
    collection->size--;
}
				`}
			</Code>
			</p>	
			
		</Slide>

		<Slide animate>		
			<p class="text-[22px]">
				<Code lang="c" lines="9-13">
				{`

void deleteItem(LotCollection *collection, int index) {
    int i;

    if (index >= collection->size) {
        printf("Index Out of Bounds\\n");
        return;
    }

	// being an array under the hood, you have to shift 
	// every element that comes next the deleted index
    for (i = index; i < collection->size; i++) {
        collection->items[i] = collection->items[i + 1];
    }
    collection->size--;
}
				`}
			</Code>
			</p>
			
		</Slide>
	</Vertical>

	<Slide>
		<p class="text-gray-500">
			everytime you delete an item, every item after the deleted one is shifted from position 
			<span class="font-mono text-white">i</span>
			to 
			<span class="font-mono text-white">i - 1</span>
			<br/>
			and this is very important
		</p>
	</Slide>

	<Vertical>
		<Slide animate>
			<p class="text-[22px]">
			<Code lang="c">
{`
// the logic behind this code is...
loaded = 0;
for (i = 0; i < harbor.supply->size; i++) {
	Lot l = getItem(harbor.supply, i);
	if (l.type == req.lot_type) {
		Stock s;

		reserve(shmem->exchange_semaphore_id);
		exchange[exchange_index(SUPPLY, harbor_index, req.lot_type)].total -= l.quantity;
		release(shmem->exchange_semaphore_id);

		write(load.write, &l, sizeof(Lot));

		deleteItem(harbor.supply, i);
		loaded += l.quantity;

		if (loaded > req.qty) break;
	}
}
write(load.write, &empty, sizeof(Lot));
`}
			</Code>
		</p>
		</Slide>

		<Slide animate>
			<p class="text-[22px]">
			<Code lang="c" lines="1">
{`
loaded = 0; // you initialize the amount loaded
for (i = 0; i < harbor.supply->size; i++) {
	Lot l = getItem(harbor.supply, i);
	if (l.type == req.lot_type) {
		Stock s;

		reserve(shmem->exchange_semaphore_id);
		exchange[exchange_index(SUPPLY, harbor_index, req.lot_type)].total -= l.quantity;
		release(shmem->exchange_semaphore_id);

		write(load.write, &l, sizeof(Lot));

		deleteItem(harbor.supply, i);
		loaded += l.quantity;

		if (loaded > req.qty) break;
	}
}
write(load.write, &empty, sizeof(Lot));
`}
			</Code>
		</p>
		</Slide>

		<Slide animate>
			<p class="text-[22px]">
			<Code lang="c" lines="3">
{`
loaded = 0; 
for (i = 0; i < harbor.supply->size; i++) {
	Lot l = getItem(harbor.supply, i); // here you extract a lot from the collection
	if (l.type == req.lot_type) { 
		Stock s;

		reserve(shmem->exchange_semaphore_id);
		exchange[exchange_index(SUPPLY, harbor_index, req.lot_type)].total -= l.quantity;
		release(shmem->exchange_semaphore_id);

		write(load.write, &l, sizeof(Lot));

		deleteItem(harbor.supply, i);
		loaded += l.quantity;

		if (loaded > req.qty) break;
	}
}
write(load.write, &empty, sizeof(Lot));
`}
			</Code>
		</p>
		</Slide>


		<Slide animate>
			<p class="text-[22px]">
			<Code lang="c" lines="4">
{`
loaded = 0; 
for (i = 0; i < harbor.supply->size; i++) {
	Lot l = getItem(harbor.supply, i); 
	if (l.type == req.lot_type) { // if it's of the type requested...
		Stock s;

		reserve(shmem->exchange_semaphore_id);
		exchange[exchange_index(SUPPLY, harbor_index, req.lot_type)].total -= l.quantity;
		release(shmem->exchange_semaphore_id);

		write(load.write, &l, sizeof(Lot));

		deleteItem(harbor.supply, i);
		loaded += l.quantity;

		if (loaded > req.qty) break;
	}
}
write(load.write, &empty, sizeof(Lot));
`}
			</Code>
		</p>
		</Slide>

		<Slide animate>
			<p class="text-[22px]">
			<Code lang="c" lines="7-9">
{`
loaded = 0; 
for (i = 0; i < harbor.supply->size; i++) {
	Lot l = getItem(harbor.supply, i); 
	if (l.type == req.lot_type) { 
		Stock s;

		reserve(shmem->exchange_semaphore_id); // you register the change in a shared memory shielded by a mutex
		exchange[exchange_index(SUPPLY, harbor_index, req.lot_type)].total -= l.quantity;
		release(shmem->exchange_semaphore_id);

		write(load.write, &l, sizeof(Lot));

		deleteItem(harbor.supply, i);
		loaded += l.quantity;

		if (loaded > req.qty) break;
	}
}
write(load.write, &empty, sizeof(Lot));
`}
			</Code>
		</p>
		</Slide>

		<Slide animate>
			<p class="text-[22px]">
			<Code lang="c" lines="11">
{`
loaded = 0; 
for (i = 0; i < harbor.supply->size; i++) {
	Lot l = getItem(harbor.supply, i); 
	if (l.type == req.lot_type) { 
		Stock s;

		reserve(shmem->exchange_semaphore_id);
		exchange[exchange_index(SUPPLY, harbor_index, req.lot_type)].total -= l.quantity;
		release(shmem->exchange_semaphore_id);

		write(load.write, &l, sizeof(Lot)); // then you send the object through a pipe

		deleteItem(harbor.supply, i);
		loaded += l.quantity;

		if (loaded > req.qty) break;
	}
}
write(load.write, &empty, sizeof(Lot));
`}
			</Code>
		</p>
		</Slide>

		<Slide animate>
			<p class="text-[22px]">
			<Code lang="c" lines="13">
{`
loaded = 0; 
for (i = 0; i < harbor.supply->size; i++) {
	Lot l = getItem(harbor.supply, i); 
	if (l.type == req.lot_type) { 
		Stock s;

		reserve(shmem->exchange_semaphore_id);
		exchange[exchange_index(SUPPLY, harbor_index, req.lot_type)].total -= l.quantity;
		release(shmem->exchange_semaphore_id);

		write(load.write, &l, sizeof(Lot));

		deleteItem(harbor.supply, i); // delete the item from your local collection
		loaded += l.quantity;

		if (loaded > req.qty) break;
	}
}
write(load.write, &empty, sizeof(Lot));
`}
			</Code>
		</p>
		</Slide>

		<Slide animate>
			<p class="text-[22px]">
			<Code lang="c" lines="14">
{`
loaded = 0; 
for (i = 0; i < harbor.supply->size; i++) {
	Lot l = getItem(harbor.supply, i); 
	if (l.type == req.lot_type) {
		Stock s;

		reserve(shmem->exchange_semaphore_id);
		exchange[exchange_index(SUPPLY, harbor_index, req.lot_type)].total -= l.quantity;
		release(shmem->exchange_semaphore_id);

		write(load.write, &l, sizeof(Lot));

		deleteItem(harbor.supply, i);
		loaded += l.quantity; // and update the loaded counter

		if (loaded > req.qty) break;
	}
}
write(load.write, &empty, sizeof(Lot));
`}
			</Code>
		</p>
		</Slide>

		<Slide animate>
			<p class="text-[22px]">
			<Code lang="c" lines="16">
{`
loaded = 0; 
for (i = 0; i < harbor.supply->size; i++) {
	Lot l = getItem(harbor.supply, i); 
	if (l.type == req.lot_type) { 
		Stock s;

		reserve(shmem->exchange_semaphore_id);
		exchange[exchange_index(SUPPLY, harbor_index, req.lot_type)].total -= l.quantity;
		release(shmem->exchange_semaphore_id);

		write(load.write, &l, sizeof(Lot));

		deleteItem(harbor.supply, i);
		loaded += l.quantity;

		if (loaded > req.qty) break; // you apply this logic until you exceed the requested quantity
	}
}
write(load.write, &empty, sizeof(Lot));
`}
			</Code>
		</p>
		</Slide>

		<Slide animate>
			<p class="text-[22px]">
			<Code lang="c" lines="2">
{`
loaded = 0; 
for (i = 0; i < harbor.supply->size; i++) { // or when you run out of items in the collection
	Lot l = getItem(harbor.supply, i); 
	if (l.type == req.lot_type) { 
		Stock s;

		reserve(shmem->exchange_semaphore_id);
		exchange[exchange_index(SUPPLY, harbor_index, req.lot_type)].total -= l.quantity;
		release(shmem->exchange_semaphore_id);

		write(load.write, &l, sizeof(Lot));

		deleteItem(harbor.supply, i);
		loaded += l.quantity;

		if (loaded > req.qty) break;
	}
}
write(load.write, &empty, sizeof(Lot));
`}
			</Code>
		</p>
		</Slide>

		<Slide animate>
			<p class="text-[22px]">
			<Code lang="c" lines="19,20">
{`
loaded = 0; 
for (i = 0; i < harbor.supply->size; i++) {
	Lot l = getItem(harbor.supply, i); 
	if (l.type == req.lot_type) { 
		Stock s;

		reserve(shmem->exchange_semaphore_id);
		exchange[exchange_index(SUPPLY, harbor_index, req.lot_type)].total -= l.quantity;
		release(shmem->exchange_semaphore_id);

		write(load.write, &l, sizeof(Lot));

		deleteItem(harbor.supply, i);
		loaded += l.quantity;

		if (loaded > req.qty) break;
	}
}
write(load.write, &empty, sizeof(Lot)); // once you've done sending the Lots, you send an empty sentinel value
										// to inform the receiver of the completed operation
`}
			</Code>
		</p>
		</Slide>
	</Vertical>

	<Slide>
		<p class="text-gray-500 font-mono">What could possibly go wrong?</p>
	</Slide>

	<Slide>
		<p class="text-gray-500 font-mono text-start">
			Well, for some reason, when asked, for example, 
			a quantity of <span class="text-white">69 tons</span>, 
			the previous algorithm was sending through the pipe 
			<span class="text-white">N &lt; 69</span>, i.e. less quantity. <br/><br/>
			Not because 69 is a nice number, mind you, but for any amount
			requested, it sent less than what it was asked for. But...why? What's 
			wrong with the previous algo?
		</p>
	</Slide>

	<Slide>
		<p class="text-gray-500 font-mono text-start text-[22px] pb-6">
			The key here was already hinted here, in the delete method of collection, remember?
		</p>

		<p class="text-[22px]">
			<Code lang="c" lines="9-11">
				{`

void deleteItem(LotCollection *collection, int index) {
    int i;

    if (index >= collection->size) {
        printf("Index Out of Bounds\\n");
        return;
    }

    for (i = index; i < collection->size; i++) {
        collection->items[i] = collection->items[i + 1]; // <-- this here is a hidden incrementer
    }
    collection->size--;
}
				`}
			</Code>
		</p>
	</Slide>

	<Vertical>
		<Slide animate>
			<p class="text-gray-500 font-mono text-start text-[22px] pb-6">
				let's now look again at the problem in the previous code:
			</p>
		</Slide>

		<Slide animate>
			<p class="text-gray-500 font-mono text-start text-[22px] pb-6">
				let's now look again at the problem in the previous code:
			</p>
			<p class="text-[22px]">
				<Code lang="c" lines="2">
	{`
	loaded = 0; 
	for (i = 0; i < harbor.supply->size; i++) { // here we increment the index
		Lot l = getItem(harbor.supply, i); 
		if (l.type == req.lot_type) { 
			Stock s;
	
			reserve(shmem->exchange_semaphore_id);
			exchange[exchange_index(SUPPLY, harbor_index, req.lot_type)].total -= l.quantity;
			release(shmem->exchange_semaphore_id);
	
			write(load.write, &l, sizeof(Lot));
	
			deleteItem(harbor.supply, i);
			loaded += l.quantity;
	
			if (loaded > req.qty) break;
		}
	}
	write(load.write, &empty, sizeof(Lot));
	`}
				</Code>
			</p>
		</Slide>

		<Slide animate>
			<p class="text-gray-500 font-mono text-start text-[22px] pb-6">
				let's now look again at the problem in the previous code:
			</p>
			<p class="text-[22px]">
				<Code lang="c" lines="13">
	{`
	loaded = 0; 
	for (i = 0; i < harbor.supply->size; i++) { 
		Lot l = getItem(harbor.supply, i); 
		if (l.type == req.lot_type) { 
			Stock s;
	
			reserve(shmem->exchange_semaphore_id);
			exchange[exchange_index(SUPPLY, harbor_index, req.lot_type)].total -= l.quantity;
			release(shmem->exchange_semaphore_id);
	
			write(load.write, &l, sizeof(Lot));
	
			deleteItem(harbor.supply, i); // and here there's a shadow increment
			loaded += l.quantity;
	
			if (loaded > req.qty) break;
		}
	}
	write(load.write, &empty, sizeof(Lot));
	`}
				</Code>
			</p>
		</Slide>
	</Vertical>

	<Slide>
		<p class="text-gray-500 font-mono text-start text-[22px] pb-6 leading-8">
			So by incrementing the index <span class="text-white">i++</span> in the for loop 
			<span class="text-white">AND</span> deleting, we are the facto incrementing 
			the index twice everytime we are dealing with a requested Lot.
			The <span class="text-white">ONLY</span> time we should increment is when
			the Lot is to be skipped. The index should remain in the same spot everytime we
			delete because of the shifting in our collection.
		</p>
	</Slide>

	<Slide>
		<p class="text-gray-500 font-mono">
			If we look at the debugged code, the increment happens once regardless of the operation made
		</p>
	</Slide>

	<Vertical>
		<Slide animate>
			<p class="text-[22px]">
			<Code lang="c">
{`
to_load = req.qty;
i = 0;
while ((i < harbor.supply->size) && (to_load > 0)) {
	Lot l = getItem(harbor.supply, i);

	if ((l.type == req.lot_type) && (l.reserved == req.ship_index)) {
		exchange[exchange_index(SUPPLY, harbor.harbor_index, req.lot_type)].reserved -= l.quantity;
		exchange[exchange_index(SUPPLY, harbor.harbor_index, req.lot_type)].total -= l.quantity;

		write(load.write, &l, sizeof(Lot));
		deleteItem(harbor.supply, i);

		to_load -= l.quantity;

	} else {
		i++;
	}
}
`}
			</Code>
		</p>
		</Slide>

		<Slide animate>
			<p class="text-[22px]">
			<Code lang="c" lines="2">
{`
to_load = req.qty; 
i = 0; // we initialize the index
while ((i < harbor.supply->size) && (to_load > 0)) {
	Lot l = getItem(harbor.supply, i);

	if ((l.type == req.lot_type) && (l.reserved == req.ship_index)) {
		exchange[exchange_index(SUPPLY, harbor.harbor_index, req.lot_type)].reserved -= l.quantity;
		exchange[exchange_index(SUPPLY, harbor.harbor_index, req.lot_type)].total -= l.quantity;

		write(load.write, &l, sizeof(Lot));
		deleteItem(harbor.supply, i);

		to_load -= l.quantity;

	} else {
		i++;
	}
}
`}
			</Code>
		</p>
		</Slide>

		<Slide animate>
			<p class="text-[22px]">
			<Code lang="c" lines="15-17">
{`
to_load = req.qty; 
i = 0;
while ((i < harbor.supply->size) && (to_load > 0)) {
	Lot l = getItem(harbor.supply, i);

	if ((l.type == req.lot_type) && (l.reserved == req.ship_index)) {
		exchange[exchange_index(SUPPLY, harbor.harbor_index, req.lot_type)].reserved -= l.quantity;
		exchange[exchange_index(SUPPLY, harbor.harbor_index, req.lot_type)].total -= l.quantity;

		write(load.write, &l, sizeof(Lot));
		deleteItem(harbor.supply, i);

		to_load -= l.quantity;

	} else {
		i++; // and increment it only when the lot was not requested
	}
}
`}
			</Code>
		</p>
		</Slide>

		<Slide animate>
			<p class="text-[22px]">
			<Code lang="c" lines="11">
{`
to_load = req.qty; 
i = 0;
while ((i < harbor.supply->size) && (to_load > 0)) {
	Lot l = getItem(harbor.supply, i);

	if ((l.type == req.lot_type) && (l.reserved == req.ship_index)) {
		exchange[exchange_index(SUPPLY, harbor.harbor_index, req.lot_type)].reserved -= l.quantity;
		exchange[exchange_index(SUPPLY, harbor.harbor_index, req.lot_type)].total -= l.quantity;

		write(load.write, &l, sizeof(Lot));
		deleteItem(harbor.supply, i); // otherwise we increment it here when the delete happens

		to_load -= l.quantity;

	} else {
		i++;
	}
}
`}
			</Code>
		</p>
		</Slide>
	</Vertical>

	<Slide>
		<p class="text-gray-500 font-mono text-start text-[22px] pb-6 leading-8">
			So in the end everything worked fine. That's it for the fancy presentation.
			<br/><br/>PS.<br/>
			I know that this would've been a trivial thing to debug with a debugger. But
			not everything is as easily debuggable, especially in a multi-process world.
			Here I obviously didn't have the chance to debug properly, so the problem 
			persisted for so long &lpar;it took about a week or so&rpar;. no printf
			shotgun method worked and only when I ported the sus logic into a toy project
			could I find the problem and refactor the method.
		</p>
	</Slide>

</Presentation>
