<script lang="ts">
	import { Slide, Code, Vertical } from '@components'
</script>

<Slide transition="fade">
    <p class="pb-6 font-mono text-gray-500">
        Let's begin with some data structures to establish some context
    </p>
</Slide>

<Slide transition="fade">
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
    <Slide animate transition="fade">	
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

    <Slide animate transition="fade">		
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

    <Slide animate transition="fade">	
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