<script lang="ts">
	import { Slide } from '@components'
</script>

<Slide transition="fade">
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
