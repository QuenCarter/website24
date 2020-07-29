<template>
    <section>
        <section id="page-title" class="padding">
            <h3 id="page-title-header">
                <img id="page-title-header-logo" src="/images/svg/24g_logo.svg" />VIDEO PLAYER
            </h3>
        </section>

        <h1 id="selected-vid-title" class="padding">{{selectedVideo.title}}</h1>

        <section class="padding" id="video-container">
            <div>                
                <video id="selectedVideo" :autoplay="allowAutoPlay" controls :src="selectedVideo.video"
                    @play="incrementViews(selectedVideo._id)" :poster="selectedVideo.thumb" />
                <section id="metrics">
                    <div id="views">
                        <h3>{{selectedVideo.views}} views</h3>
                    </div>
                    <div id="thumbs">
                        <h3>
                            <img src="/images/svg/thumbs_up.svg" @click="incrementLikes(selectedVideo._id)"
                                class="icon" />
                            {{selectedVideo.thumbsUp}}
                        </h3>
                        <h3>

                            <img src="/images/svg/thumbs_down.svg" @click="incrementDislikes(selectedVideo._id)"
                                class="icon" />
                            {{selectedVideo.thumbsDown}}
                        </h3>
                    </div>
                </section>
            </div>

            <div id="videoSidebar">
                <img v-for="video in videoArray" :key="video.index" :src="video.thumb" @click="changeVidToPlay(video)"
                    class="vid-thumb" :class="[video._id === selectedVideo._id ? 'vid-thumb-active' : '']" />
            </div>
            <section id="comments-container">
                <h3>Comments</h3>

                <div>
                    <textarea v-model="commentToAdd" placeholder="Type up a sweet comment ..." rows="2"></textarea>
                </div>
                <button class="addBtn" @click="addcomment(selectedVideo._id, commentToAdd)">add comment</button>
                <CommentBlock :comment="comment" v-for="comment in comments" :key="comment._id" />
            </section>
        </section>

    </section>
</template>

<script>
    import axios from "axios";
    import moment from 'moment';
    import CommentBlock from './CommentBlock.vue';

    const url = "http://localhost:3000";

    const getVideos = async () => {
        try {
            let response = await axios.get(url);
            return response.data;
        } catch (error) {
            console.error(error);
        }
    }
    const getComments = async (id) => {
        try {
            let response = await axios.get(`${url}/videos/${id}/comments`);
            return response.data;

        } catch (error) {
            console.error(error);
        }
    };

    export default {
        data: function () {
            return {
                videoArray: [],
                selectedVideo: {
                    video: "",
                    thumb: "",
                    title: "",
                    thumbsUp: 0,
                    thumbsDown: 0,
                    views: 0,
                    _id: ""
                },
                comments: [],
                allowAutoPlay: false,
                commentToAdd: ""

            }
        },
        components: {
            CommentBlock
        },
        mounted: async function () {

            let videos = await getVideos();
            this.videoArray = videos;
            this.selectedVideo = videos[0];
            this.loadComments(this.selectedVideo._id);
        },

        methods: {
            changeVidToPlay: function (video) {
                this.selectedVideo = video;
                this.allowAutoPlay = true;
            },
            incrementLikes: async function (id) {
                try {
                    let response = await axios.post(`${url}/videos/${id}/thumbsUp`);
                    this.selectedVideo.thumbsUp = response.data.thumbsUp;
                } catch (error) {
                    console.error(error);
                }
            },
            incrementDislikes: async function (id) {
                try {
                    let response = await axios.post(`${url}/videos/${id}/thumbsDown`);
                    this.selectedVideo.thumbsDown = response.data.thumbsDown;
                } catch (error) {
                    console.error(error);
                }
            }
            ,
            incrementViews: async function (id) {
                try {
                    let response = await axios.post(`${url}/videos/${id}/views`);
                    this.selectedVideo.views = response.data.views;
                } catch (error) {
                    console.error(error);
                }
            },
            addcomment: async function (id, commentToAdd) {
                try {
                    await axios.post(`${url}/videos/${id}/comments`, { comment: commentToAdd, videoId: id });
                    this.loadComments(id);
                    this.commentToAdd = "";

                } catch (error) {
                    console.error(error);
                }
            },
            loadComments: async function (id) {
                try {
                    let apiComments = await getComments(id);
                    let sortedcomments = apiComments.sort((a, b) => parseInt(b.date) - parseInt(a.date));
                    this.comments = sortedcomments.map(c => {
                        let str = moment(parseInt(c.date)).from(Date.now());
                        let newComment = { ...c, dateString: str };
                        return newComment;
                    });

                } catch (error) {
                    console.error(error);
                }
            }
        }


    }
</script>

<style scoped>
    #page-title-header {
        margin: 0;
    }

    #page-title {
        color: white;
        background-color: #393C3E;
        padding-top: 5vh;
        padding-bottom: 5vh;
        font-size: xx-large;
    }

    .vid-thumb {
        max-width: 75%;
        min-width: 15vw;
        margin-bottom: 2vw;
        box-sizing: border-box;
    }

    .vid-thumb-active {
        border: .5em solid #FAA61A;
    }

    .icon {
        min-width: 2rem;
        vertical-align: middle;
    }

    #page-title-header-logo {
        width: 10rem;
        vertical-align: middle;
        padding-right: 2vw;
    }

    #selected-vid-title {
        font-size: xx-large;
    }

    #selectedVideo {
        width: 70vw;
    }

    #video-container {
        display: grid;
        grid-template: auto auto / auto auto;
    }

    #videoSidebar {
        margin-left: 2vw;
        display: flex;
        flex-direction: column;
    }

    #metrics {
        display: flex;
        justify-content: space-between;
        align-items: center;
        border-bottom: 2px solid #d1d1d1;
        max-width: 70vw;
        font-size: 1.5rem;
    }

    #thumbs {
        display: flex;
        justify-content: space-between;
        width: 8vw;
        color: #E58C1B;
    }

    #comments-container {
        max-width: 70vw;
    }

    textarea {
        resize: none;
        width: 100%;
        border: 2px solid #d1d1d1;
        padding: 1em;
        box-sizing: border-box;
        font-size: 1.5rem;
        font-family: "Brandon_Light";

    }

    textarea:focus {
        outline: 2px solid #E58C1B;
        border: 2px solid white;
    }

    .addBtn {
        background-color: #FAA61A;
        font-family: "Brandon_Light";
        font-size: 18px;
        letter-spacing: .05em;
        padding: 15px 45px;
        color: white;
        line-height: 10px;
        border: none;
        margin-top: 3vh;
        margin-bottom: 2vh;



    }

    .addBtn:hover {
        background-color: #E58C1B;
    }

    .addBtn:active,
    .addBtn:focus {
        border: none !important;
        outline: none;
    }

    #selected-vid-title {
        margin-top: 10vh;
        margin-bottom: 5vh;
    }

    @media only screen and (min-width: 375px) and (max-width: 600px) {
        #page-title {
            display: grid;
            place-items: center;
            font-size: large;
        }

        #page-title-header-logo {
            display: block;
            margin-bottom: 3vh;
            width: 6rem;
            margin-left: auto;
            margin-right: auto;
        }

        #video-container {
            display: flex;
            flex-direction: column;
        }

        #videoSidebar {
            display: flex;
            flex-direction: row;
            margin-top: 3rem;
        }

        #selected-vid-title {
            margin-top: 5vh;
            margin-bottom: 2vh;
        }

        #selectedVideo {
            width: 90vw;
        }

        .vid-thumb {
            margin-right: 2vw;
            width: 10rem;
        }

        .vid-thumb-active {
            border: .25rem solid #FAA61A;
        }
      .icon{
         min-width: 1.5rem;
      }

      #metrics {
        font-size: 1rem;
        max-width: 90vw;
      }

      #thumbs{                      
          min-width: 30%;          
      }

      #comments-container {
        max-width: 90vw;
    }

    }
</style>