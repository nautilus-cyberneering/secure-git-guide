<div class="contact" id="contact">
    <div class="contact-text">
        <h3>Suggestions and Contact</h3>
        <p>If you would like to contact us or make any suggestions or comments, please do so either via creating a new issue in our <a href='https://github.com/Nautilus-Cyberneering/secure-git-guide/issues'>GitHub repository</a> or by emailing us at <a href='mailto:info@nautilus-cyberneering.de'>info@nautilus-cyberneering.de</a>.</p>
    </div>
    <div class="contact-img">
        <img src="https://images.unsplash.com/photo-1517048676732-d65bc937f952?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1470&q=80" alt="">
    </div>
</div>

<style>
    .contact{
        background-color: #e6e6fa	;
        padding: 2rem;
        margin-top: 1.5rem;
    }

    p{
        margin-top: 1.5rem;
    }

    img{
        display: none;
    }

    @media (min-width: 768px){
        .contact{
            display: flex;
            gap: 2rem;
        }

        .contact-text{
            flex: 1 1 0px;
        }

        .contact-img{
            flex: 1 1 0px;
        }

        img{
            display: block;
        }
    }

    @media (min-width: 1000px){
        h3{
            font-size: 2.2rem;
        }

        p{
            font-size: 1.5rem;
        }
    }
</style>