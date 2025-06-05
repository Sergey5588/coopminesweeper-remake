

<script>
    import Game from "$lib/Game.svelte";
    import { io} from "socket.io-client";
    
    const socket = io('wss://api-mines.vec3.org:55588');
    let room_name = $state("");
    let last_room = "";
    let current_room = "";
    let m = $state({x:0, y:0});
    let players = $state([]);


    socket.on('ping',function(data){
        console.log(data);
    });

    socket.on('team_data', function(clients){
        players = Array.from(clients);
    });

    function get_teammates() {
        socket.emit('get_teammates', current_room);
    }

    function mouse_pos(event) {
        m.x = event.clientX;
        m.y = event.clientY;
        socket.emit("mouse_pos_report", {x: event.clientX, y:event.clientY},  current_room);
    }


    function enter_room() {
        current_room = room_name
        socket.emit("leave", last_room);
        socket.emit("create", current_room);
        last_room = current_room;
    }
    
</script>

<svelte:window on:mousemove={mouse_pos} />
<main>
    <h1>Welcome to ShareSweeper!</h1>
    

    <input placeholder="room name" bind:value={room_name}>
    <button onclick={enter_room}>Enter</button>

    <button onclick={() => {socket.emit("ping", current_room)}}>Ping</button>
    <button onclick={get_teammates}> Get your team</button>

    <Game>

    </Game>
    {#each players as plr}
        <h1>{plr.mouse_x} + {plr.mouse_y}</h1>
        <button style="position: absolute; top: {plr.mouse_y+40}px; left: {plr.mouse_x+40}px">follower + {plr.id.substr(0,2)}</button>       
    {/each}
</main>



