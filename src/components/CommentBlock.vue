<template>
    <div class="comment">
        <img src="/images/png/placeholder_avatar.png" class="avatar" />

        <div>
            <strong>{{comment.name}}
                <span class="dateString">{{comment.dateString}}</span></strong>
            <p>{{comment.comment}}</p>
        </div>
    </div>
</template>

<script>

    export default {
        props: {
            comment: Object
        }
    }
</script>

<style>
    .avatar {
        max-width: 5%;
        margin-right: 1vw;
        align-self: start
    }

    .comment {
        display: flex;
        margin-bottom: 1vh;
    }

    .dateString {
        color: #d1d1d1;
    }

    @media only screen and (min-width: 375px) and (max-width: 600px) {
        .avatar {
            margin-right: 5vw;
            max-width: 20vw;
        }

        .dateString {
            display: block;
        }

    }
</style>